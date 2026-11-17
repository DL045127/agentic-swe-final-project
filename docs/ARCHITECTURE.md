# Architecture & Design: Natural-Language Photo Search

**Course:** CS 378AC — Final Project
**Scope:** How the system is structured, the data that flows through it, and the key design decisions. Companion to `PROPOSAL.md` and `REQUIREMENTS.md`.

---

## 1. System Overview

A single-machine application with three layers:

1. **Indexing** — reads a folder of images, generates a CLIP embedding per image, and writes an on-disk index.
2. **Search** — embeds a text query with CLIP and ranks indexed images by cosine similarity.
3. **UI** — a Streamlit app for importing, searching, and viewing results.

```
┌──────────────────────────────────────────────────────────────┐
│                      Streamlit UI (app.py)                     │
│   import folder · enter query · ranked thumbnails · open image │
└───────────────┬───────────────────────────┬───────────────────┘
                │                           │
        ┌───────▼────────┐          ┌────────▼────────┐
        │   Indexer      │          │    Searcher     │
        │  build/update  │          │  query → rank   │
        └───────┬────────┘          └────────┬────────┘
                │                           │
                │     ┌───────────────┐     │
                └────▶│  CLIP encoder │◀────┘
                      │ (HF + Torch)  │
                      └───────┬───────┘
                              │
                     ┌────────▼─────────┐
                     │  Index store     │
                     │ embeddings +     │
                     │ path manifest    │
                     └──────────────────┘
```

---

## 2. Components

### 2.1 CLIP Encoder (`clip_encoder.py`)
Wraps Hugging Face `CLIPModel` + `CLIPProcessor` for `openai/clip-vit-base-patch32`.
- `encode_images(list[PIL.Image]) -> np.ndarray` — L2-normalized image embeddings.
- `encode_text(str) -> np.ndarray` — L2-normalized text embedding.
- Loads the model once and reuses it (expensive to construct).
- Records the exact model revision for reproducibility.

### 2.2 Indexer (`indexer.py`)
- Walks a folder for supported extensions (`.jpg/.jpeg/.png/.webp/...`).
- Opens each image with Pillow; **skips and logs** unreadable/corrupt files (edge case).
- Batches images through the encoder.
- Persists embeddings plus a parallel manifest of absolute paths and a content hash.
- Supports incremental reuse: if a cached index exists and the image set is unchanged, skip re-embedding.

### 2.3 Index Store (`index_store.py`)
On-disk persistence of the index.
- **Format:** NumPy `.npy` for the embedding matrix (`N × D`, float32) + a JSON/`.json` sidecar manifest (ordered list of image paths, per-image hash, model revision, embedding dim).
- **Invariant:** row *i* of the embedding matrix corresponds to manifest entry *i*. This association is what the "cached embeddings stay with the correct photo" check verifies.
- Stores a fingerprint (model revision + sorted file list hash) so a stale index can be detected and rebuilt.

### 2.4 Searcher (`searcher.py`)
- Embeds the query text.
- Computes cosine similarity against the embedding matrix (a single normalized dot product since vectors are L2-normalized).
- Returns the top-*k* `(path, score)` pairs in descending score order.

### 2.5 UI (`app.py`)
Streamlit app:
- Folder input + "Build / load index" action with progress and timing.
- Query box; renders top-*k* thumbnails with scores.
- Click a thumbnail to open the original.
- Surfaces indexing time and query latency (measurement for DR-1 decision 2).

### 2.6 Evaluation (`eval/`)
Independent of the app runtime (VR-2):
- `queries.json` / `labels.json` — 15 queries with human relevance labels, committed **before** tuning; **off-limits to agents**.
- `baseline.py` — filename case-insensitive keyword-overlap ranker.
- `evaluate.py` — computes precision@5 for CLIP search and the baseline; prints a comparison table.

---

## 3. Data Model

**Image record (manifest entry):**
```json
{ "index": 0, "path": "images/wiki_00123.jpg", "sha256": "…", "source_url": "…", "license": "CC0" }
```

**Index fingerprint:**
```json
{ "model_revision": "…", "embedding_dim": 512, "num_images": 212, "fileset_hash": "…" }
```

**Embeddings:** `embeddings.npy`, shape `(N, 512)`, float32, L2-normalized, row-aligned to the manifest.

**Source manifest (`images/MANIFEST.csv`):** path, source URL, license (CC0), attribution — supports RR-3 and reproducibility.

---

## 4. Primary Data Flow

### Indexing
```
folder → list image files → open with Pillow (skip corrupt)
      → batch → CLIP.encode_images → L2-normalize
      → write embeddings.npy + manifest.json + fingerprint
```

### Search
```
query string → CLIP.encode_text → L2-normalize
            → cosine sim vs embeddings matrix
            → argsort desc → top-k (path, score)
            → UI renders thumbnails
```

### Startup reuse
```
on launch → read fingerprint → matches current model + fileset?
          → yes: load cached index (fast path)
          → no:  rebuild index
```

---

## 5. Key Design Decisions

### DR-1 Decision 1 — Semantic (CLIP) vs. filename search
- **Problem:** rank photos by meaning, not filename.
- **Choice:** CLIP ViT-B/32 shared-space embeddings + cosine similarity.
- **Alternative considered:** pure filename keyword-overlap search (also implemented as the baseline).
- **Evidence:** precision@5 over 15 labeled queries; target ≥ 0.60 and ≥ 0.20 above baseline.

### DR-1 Decision 2 — Persistent embedding cache vs. recompute on startup
- **Problem:** re-embedding every launch is slow.
- **Choice:** persist embeddings + manifest; reuse when the fingerprint matches.
- **Alternative considered:** recompute in memory each run (simpler, no cache-correctness risk).
- **Evidence:** measured indexing time vs. cached-startup latency; restart-consistency check.

### Similarity: brute force vs. vector database
- **Choice:** brute-force normalized dot product over an in-memory matrix.
- **Rationale:** at 100–300 images (and even a few thousand), a single NumPy matmul is sub-millisecond; a vector DB adds dependencies and operational complexity with no benefit at this scale.
- **Known limitation:** does not scale to very large collections without an ANN index.

---

## 6. Error Handling & Invariants

- **Corrupt / unreadable images:** caught at open time, skipped, and logged; indexing continues. (FR-2 edge case.)
- **Empty / whitespace query:** returns no results with a message rather than erroring.
- **Row–manifest alignment:** embedding row *i* ↔ manifest entry *i* is treated as a hard invariant; a mismatch is a defect the restart-consistency and labeled-query checks detect.
- **Stale index:** fingerprint mismatch (model revision or fileset change) forces a rebuild.
- **Determinism:** same images + same model revision ⇒ same rankings for a given query.

---

## 7. Technology Stack

| Concern | Choice | Notes |
|---|---|---|
| Language | Python | |
| Model | CLIP ViT-B/32 | `openai/clip-vit-base-patch32`, revision pinned |
| Inference | Hugging Face Transformers + PyTorch | local inference, no training |
| Image I/O | Pillow | open, decode, thumbnail |
| Similarity | NumPy | L2-normalize + dot product |
| UI | Streamlit | import, search, gallery |
| Persistence | `.npy` + JSON | embeddings + manifest + fingerprint |

---

## 8. Directory Layout (planned)

```
agentic-final-proj/
├── app.py                 # Streamlit UI
├── src/
│   ├── clip_encoder.py
│   ├── indexer.py
│   ├── index_store.py
│   └── searcher.py
├── eval/
│   ├── queries.json       # committed before tuning
│   ├── labels.json        # ground truth; off-limits to agents
│   ├── baseline.py
│   └── evaluate.py
├── images/                # CC0 demo set
│   └── MANIFEST.csv       # source + license per image
├── index/                 # generated: embeddings.npy, manifest.json, fingerprint.json
├── docs/
│   ├── PROPOSAL.md
│   ├── REQUIREMENTS.md
│   ├── ARCHITECTURE.md
│   ├── ATTRIBUTION.md     # planned
│   ├── AGENT_LOG.md       # planned
│   └── DEBUGGING.md       # planned
├── requirements.txt       # pinned versions
└── README.md              # setup + reproduce demo + run validation
```

---

## 9. Reproducibility Hooks

- Pinned dependency versions (`requirements.txt`) and recorded CLIP model revision.
- Committed CC0 images + `MANIFEST.csv` so the demo runs offline from a clean checkout.
- `eval/evaluate.py` reproduces the headline precision@5 claim and the baseline comparison with one command.
- Fingerprint file makes index staleness explicit and rebuilds reproducible.
