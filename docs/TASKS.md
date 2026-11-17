# Project Tasks

Working task list for Natural-Language Photo Search, organized by the milestones in `REQUIREMENTS.md` (§9).

**Legend:** `[x]` done · `[ ]` todo · 👤 = human-only (must not be delegated to an agent) · 🤖 = good candidate for an agent

---

## Done so far
- [x] Write proposal (`docs/PROPOSAL.md`)
- [x] Write requirements traceability (`docs/REQUIREMENTS.md`)
- [x] Write architecture/design (`docs/ARCHITECTURE.md`)
- [x] Write high-level README
- [x] Scaffold doc stubs (`ATTRIBUTION.md`, `AGENT_LOG.md`, `DEBUGGING.md`)
- [x] Scaffold eval fixtures (`eval/queries.json`, `eval/labels.json`)
- [x] Scaffold `images/MANIFEST.csv`, `requirements.txt`, `.gitignore`
- [x] Initial checkpoint committed and pushed

---

## E0 — Contract (claim, reproducible setup, initial acceptance check)
- [ ] 👤 Assemble ~100–300 CC0 images from Wikimedia Commons
- [ ] 👤 Fill `images/MANIFEST.csv` with source URL + license per image
- [ ] 👤 Write the real 15 queries in `eval/queries.json` (**before** any tuning)
- [ ] 👤 Label relevant images in `eval/labels.json`, ≥ 5 relevant per query (**before** any tuning)
- [ ] 👤 Verify Python environment; pin exact versions via `pip freeze` into `requirements.txt`
- [ ] 👤 Record the exact CLIP model revision in `docs/ATTRIBUTION.md`
- [ ] 🤖 Implement `eval/baseline.py` (case-insensitive filename keyword-overlap ranker)
- [ ] 🤖 Implement `eval/evaluate.py` (precision@5 for a ranking vs. labels; mean over queries)
- [ ] Establish the filename baseline precision@5 number (fixed comparison point)

## E1 — Behavior (core workflow + edge case/invariant)
- [ ] 🤖 Implement `src/clip_encoder.py` (load CLIP once; `encode_images`, `encode_text`; L2-normalize)
- [ ] 🤖 Implement `src/indexer.py` (walk folder; skip + log corrupt images; batch encode)
- [ ] 🤖 Implement `src/index_store.py` (save/load `embeddings.npy` + manifest + fingerprint)
- [ ] 🤖 Implement `src/searcher.py` (embed query; cosine similarity; top-k)
- [ ] 🤖 Implement `app.py` (Streamlit: import, query, ranked thumbnails, open original)
- [ ] 👤 Run CLIP precision@5 on the labeled queries; compare to baseline
- [ ] 👤 Decide whether targets are met (P@5 ≥ 0.60 and ≥ 0.20 over baseline); record result
- [ ] Demonstrate edge case: corrupt image skipped without crashing
- [ ] Demonstrate invariant: index rebuild / cached-reload yields identical rankings (restart consistency)

## E2 — Control (agent documentation + human review)
- [ ] 👤 Record ≥ 2 agent-work episodes in `docs/AGENT_LOG.md` (Request → Change → Observation → Human decision)
- [ ] 👤 Ensure ≥ 1 episode shows a consequential correction, rejection, or redesign
- [ ] 👤 Fill student vs. agent contributions in `docs/ATTRIBUTION.md`
- [ ] 👤 Independently inspect/execute each important agent change before accepting (AR-3)

## E3 — Defense (independent validation, reproducible demo, limitations)
- [ ] 🤖 Add a mutation-sensitivity check (e.g., shuffled/misaligned ranking fails the eval)
- [ ] 👤 Write the debugging story in `docs/DEBUGGING.md` (Symptom → Cause → Fix → Verification)
- [ ] 👤 Commit a pre-built index for the offline demo (`git add -f index/...`)
- [ ] Finalize README setup/run/reproduce instructions; verify from a clean checkout
- [ ] Fill DR-1 decision outcomes in `docs/ARCHITECTURE.md` (evidence + what was retained/changed)
- [ ] Measure and record indexing time and query latency
- [ ] Prepare a difficult/failure query to show a limitation

## Submission (REQUIREMENTS.md §10)
- [ ] 👤 Confirm instructor + TA have repo access
- [ ] 👤 Verify clean-checkout run end to end
- [ ] 👤 Build the ~6-slide deck PDF (Overview, Contributions, Decision 1, Decision 2, Debugging, Limitations)
- [ ] 👤 Submit repo URL + slide deck via Canvas
- [ ] 👤 Reserve a demonstration slot
