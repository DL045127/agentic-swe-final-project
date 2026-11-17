# Natural-Language Photo Search

Search a local photo collection with plain-English descriptions instead of filenames. Point the app at a folder, let it index the images, then type something like *"a dog on a beach"* or *"friends eating outside"* and get back a ranked gallery of matching photos.

> **Status:** Early stage. Planning and design documents are complete; the application is not yet implemented. Sections marked **(planned)** describe intended behavior, not working code.

---

## Why

Finding a photo usually means remembering *what it looks like*, not its filename or date. This project explores how well a pretrained image–text model ([CLIP](https://huggingface.co/openai/clip-vit-base-patch32)) can turn that visual memory into useful search results, entirely on a local machine.

**Intended user:** someone with a personal photo library (hundreds of images) saved under uninformative names like `IMG_4821.jpg`.

---

## How it works

The app embeds every image and the text query into CLIP's shared vector space, then ranks images by cosine similarity to the query.

```
import folder → index images (CLIP embeddings, cached)
             → type a text query
             → ranked thumbnails by similarity
             → click to open the original
```

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) for the component breakdown, data model, and design decisions.

---

## Core workflow

1. Point the app at a folder of images.
2. The app indexes the images and caches the embeddings on disk.
3. Type a natural-language description.
4. Review ranked thumbnails ordered by relevance.
5. Open a result to view the original photo.

---

## The claim this project is built to defend

CLIP-based semantic search achieves **average precision@5 ≥ 0.60** on 15 pre-labeled queries, beating a filename-keyword baseline by **≥ 0.20 absolute points** on the same queries.

The relevance labels are committed **before** any tuning and are off-limits to coding agents, giving an implementation-independent ground truth. The filename baseline is a separate, simpler method for differential comparison.

---

## Tech stack

| Concern | Choice |
|---|---|
| Language | Python |
| Model | CLIP ViT-B/32 (`openai/clip-vit-base-patch32`, revision pinned) |
| Inference | Hugging Face Transformers + PyTorch (local, no training) |
| Image I/O | Pillow |
| Similarity | NumPy (L2-normalize + dot product) |
| UI | Streamlit |
| Persistence | `.npy` embeddings + JSON manifest |

Brute-force similarity is used deliberately: at 100–300 images a single NumPy matmul is sub-millisecond, so a vector database would add dependencies with no benefit at this scale.

---

## Repository layout (planned)

```
agentic-final-proj/
├── app.py                 # Streamlit UI
├── src/
│   ├── clip_encoder.py    # CLIP image/text embedding wrapper
│   ├── indexer.py         # folder walk + batch embedding
│   ├── index_store.py     # persist/load embeddings + manifest
│   └── searcher.py        # query → ranked results
├── eval/
│   ├── queries.json       # 15 queries (committed before tuning)
│   ├── labels.json        # ground-truth relevance (off-limits to agents)
│   ├── baseline.py        # filename keyword-overlap ranker
│   └── evaluate.py        # precision@5 for CLIP vs. baseline
├── images/
│   └── MANIFEST.csv       # CC0 demo images: source + license per file
├── index/                 # generated: embeddings.npy, manifest.json, fingerprint.json
├── docs/
│   ├── PROPOSAL.md
│   ├── REQUIREMENTS.md
│   ├── ARCHITECTURE.md
│   ├── ATTRIBUTION.md     # planned
│   ├── AGENT_LOG.md       # planned
│   └── DEBUGGING.md       # planned
├── requirements.txt       # pinned versions (planned)
└── README.md
```

---

## Setup (planned)

> Not yet implemented. Intended flow:

```bash
# 1. Create and activate a virtual environment
python -m venv .venv && source .venv/bin/activate

# 2. Install pinned dependencies
pip install -r requirements.txt

# 3. Launch the app
streamlit run app.py
```

The demo image set and a pre-built index will be committed so the demo runs **offline** from a clean checkout. The CLIP model downloads once on first setup, then is cached locally.

---

## Reproducing the evaluation (planned)

```bash
python eval/evaluate.py
```

Prints precision@5 for CLIP semantic search and the filename baseline over the 15 labeled queries, so the headline claim is reproducible with one command.

---

## Demo collection & licensing

The demo uses ~100–300 photos from [Wikimedia Commons](https://commons.wikimedia.org/), restricted to **CC0** images. `images/MANIFEST.csv` records each image's source URL and license for reproducibility and attribution.

---

## Scope

| Category | Items |
|---|---|
| **Core** | Folder import, embedding + persistent index, text query, ranked thumbnails, open original. |
| **Optional** | Live folder updates, search-by-image, similarity-threshold tuning UI. |
| **Known limitations** | Counting, fine spatial relationships, text-in-image (OCR), face identity; very large collections (brute-force similarity). |

**Fallback:** if the full scope runs long, ship one fixed, pre-indexed collection with natural-language search and a thumbnail gallery.

---

## Documentation

| Document | Contents |
|---|---|
| [`docs/PROPOSAL.md`](docs/PROPOSAL.md) | Project proposal (artifact, demo/validation, human–agent roles, risks/fallback). |
| [`docs/REQUIREMENTS.md`](docs/REQUIREMENTS.md) | Full requirements traceability and acceptance checklists. |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Components, data model, data flow, design decisions, invariants. |

---

## Attribution

Reused: pretrained CLIP ViT-B/32, Hugging Face Transformers, PyTorch, Pillow, NumPy, Streamlit (all attributed with pinned versions in `docs/ATTRIBUTION.md`, planned). Demo images are CC0 from Wikimedia Commons. No course homework material is used anywhere in this repository.
