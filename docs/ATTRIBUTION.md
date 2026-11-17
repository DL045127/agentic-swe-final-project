# Attribution & Contributions

Satisfies **RR-3**. Records reused components (with versions/commits), student-added work, and significant AI-agent contributions. Distinguishes original work from reused and agent-generated work.

> **Status:** Scaffold. Fill version/revision columns at milestone **E0** once the environment is verified (`pip freeze`) and the CLIP model revision is recorded.

---

## 1. External libraries & frameworks

| Component | Purpose | Version / commit | License | Source |
|---|---|---|---|---|
| PyTorch | Model inference backend | `TODO` | BSD-3-Clause | https://pytorch.org/ |
| Hugging Face Transformers | CLIP model loading & processing | `TODO` | Apache-2.0 | https://github.com/huggingface/transformers |
| Pillow | Image decode / thumbnails | `TODO` | HPND / MIT-CMU | https://python-pillow.org/ |
| NumPy | Similarity computation | `TODO` | BSD-3-Clause | https://numpy.org/ |
| Streamlit | UI | `TODO` | Apache-2.0 | https://streamlit.io/ |

## 2. Reused model

| Model | Revision (commit hash) | License | Source |
|---|---|---|---|
| CLIP ViT-B/32 (`openai/clip-vit-base-patch32`) | `TODO` | MIT | https://huggingface.co/openai/clip-vit-base-patch32 |

> Record the exact revision used, e.g. the commit from `huggingface_hub` snapshot metadata, so inference is reproducible.

## 3. Demo data

| Dataset | Count | License | Source | Manifest |
|---|---|---|---|---|
| Wikimedia Commons selection | ~100–300 | CC0 | https://commons.wikimedia.org/ | `images/MANIFEST.csv` |

## 4. Student contributions

Original work by the student (not reused, not agent-generated):

- `TODO` — e.g., project scope, architecture, evaluation design, relevance labels, acceptance criteria.

## 5. AI-agent contributions

Significant work produced by AI agents (cross-reference `docs/AGENT_LOG.md`):

- `TODO` — summarize per episode: what was generated, and the human decision (accepted / corrected / rejected / redesigned).

## 6. Prohibited material statement

No CS 378AC homework material (starter code, solutions, tests, data, or handouts) appears in this repository or its history.
