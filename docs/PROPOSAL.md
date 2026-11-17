# Project Proposal: Natural-Language Photo Search

**Course:** CS 378AC — Final Project
**Status:** Proposal (subject to change; significant scope changes will be recorded and justified)

---

## 1. Proposed Artifact and Workflow (PR-1)

### What will you make?

I will build a **local photo search application** for people who have large collections of photos with unhelpful filenames. Users import a folder, let the application index its images, and search using natural-language descriptions such as "friends eating outside" or "a dog on a beach." Results appear as ranked thumbnails that users can open to view the original photos.

### Who is the intended user?

Someone with a personal photo library (hundreds of images) saved under uninformative names like `IMG_4821.jpg`, who wants to find a specific photo from memory of *what it looks like* rather than its filename or date.

### Core user workflow

1. Point the app at a folder of images.
2. The app indexes the images (generates and caches embeddings).
3. The user types a natural-language description.
4. The app returns ranked thumbnails ordered by relevance.
5. The user opens a result to view the original photo.

### Why it is relevant

Finding a photo usually means remembering what it looks like without remembering its filename or date. This project explores how well a pretrained image–text model can turn that visual memory into useful search results.

### Observable result

A ranked gallery of thumbnails responding to a free-text query, where the top results visibly match the described content, and selecting a thumbnail opens the original image.

### Model choice

Semantic search uses [OpenAI's CLIP ViT-B/32](https://huggingface.co/openai/clip-vit-base-patch32) via the [Hugging Face Transformers library](https://huggingface.co/docs/transformers/model_doc/clip). CLIP produces image and text embeddings in a shared vector space, so photos can be ranked by cosine similarity to a text query. CLIP supports local inference without training a new model and is light enough for this collection size.

---

## 2. Demonstration and Validation (PR-2)

### How the project will be demonstrated

The live demo uses a prepared collection of roughly **100–300 photos** from [Wikimedia Commons](https://commons.wikimedia.org/), restricted to images with verified **CC0 licensing**. The images and a manifest recording their sources and licenses are committed to the repository so graders can reproduce the demo without a private photo collection.

The demo will:
- Import the collection.
- Search several descriptions and open relevant results.
- Restart the application to show the saved index is reused.
- Run locally without network access.

### Expected result of the primary workflow

For a well-formed query, the top-ranked thumbnails visibly depict the described subject, and the saved index makes a restart fast (no re-embedding).

### Acceptance check that can expose an incorrect result

I will prepare **15 queries** and manually label the relevant photos **before** tuning the system. Target: **average precision@5 ≥ 0.60**, exceeding a filename-search baseline by at least **0.20 absolute points** on the same 15 queries. The baseline ranks photos by case-insensitive keyword overlap between queries and original filenames. Both methods' results will be reported even if targets are not met. Each scored query will have at least five relevant photos so precision@5 is not capped by insufficient matches.

I will also measure indexing time and query latency, and demonstrate a difficult query (e.g., counting or spatial relationships) or one with no relevant photos to expose the model's limits.

### How the expected result is determined independently

The relevance labels are created and committed **before** any evaluation or tuning, and **coding agents are not permitted to modify them**. This gives an implementation-independent ground truth. The filename baseline is a separate, simpler method providing a differential comparison.

---

## 3. Human and Agent Responsibilities (PR-3)

### Decisions I will own

Project scope, architecture, model selection, relevance labels, and acceptance criteria. Two decisions I will evaluate:
1. Whether CLIP-based semantic search improves retrieval over filename search.
2. Whether storing embeddings in a persistent cache provides worthwhile startup savings.

### Tasks that may be delegated to agents

Bounded tasks such as image loading, embedding generation, index persistence, and the results interface.

### How agent changes will be inspected and validated

I will review each change and verify output using the fixed labeled queries and small, controlled image collections. Checks include: corrupt files are handled, cached embeddings stay associated with the correct photos, and search results stay consistent after restart. AI-generated code will not be accepted solely because the agent reports success.

### How human oversight is documented

I will keep agent tasks, relevant commits, evaluation results, and an actual debugging example in the repository, so I can explain what I accepted, corrected, or rejected. The evaluation labels stay under my control as an independent basis for judging agent-generated implementations.

---

## 4. Dependencies, Risks, and Fallback (PR-4)

### Planned dependencies and reused sources

- Pretrained **CLIP ViT-B/32** via **Hugging Face Transformers** + **PyTorch** (reused, attributed; exact model revision recorded).
- **Python**, **Pillow** (image handling), **NumPy** (similarity), **Streamlit** (interface).
- Images from **Wikimedia Commons** (CC0 only), with a committed source/license manifest.
- No course homework material will be used.

### External dependencies and obstacles

Model download on first setup (then cached for offline demo); dependency version drift (pinned versions recorded).

### Highest-risk functionality

CLIP may perform poorly on detailed descriptions, counting, or spatial relationships. I will evaluate a small sample early, before investing heavily in the interface. For this collection size, I will use a straightforward similarity calculation rather than a separate vector database.

### Smaller viable implementation (fallback)

If implementation takes longer than expected, the fallback supports **one fixed, pre-indexed photo collection** with natural-language search and a thumbnail gallery. Folder updates, OCR, face identification, and search-by-image remain outside that smaller scope.

---

## Starting Point

A fresh repository reusing the pretrained CLIP ViT-B/32 model through Hugging Face Transformers and PyTorch. I will record the exact model revision, dependency versions, and reused sources. I will first assemble the publicly reproducible photo collection, commit the evaluation queries and relevance labels, and establish the filename-search baseline, providing a fixed comparison before implementing or adjusting semantic retrieval.
