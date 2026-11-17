# Project instructions for agents

These instructions apply throughout this repository. Follow explicit user instructions for the current task; do not infer permission to change project requirements or human-owned evaluation data from a general implementation request.

## Project and current state

This CS 378AC final project is a local natural-language photo search application. The core workflow is **import folder → index images → text query → ranked thumbnails → open original**. The initial collection is approximately 100–300 photos; the smallest fallback is one fixed, pre-indexed collection with search and a gallery.

At this file's creation, the repository contains planning documents and scaffolds, not a runnable application. Application modules, tests, real demo data, evaluation results, and submission PDFs are still pending. Inspect the current checkout before relying on this snapshot. Planned file trees, commands, targets, and examples are not proof of implementation or successful validation.

## Read the relevant project documents

Before changing the project, read [the requirements](docs/REQUIREMENTS.md), inspect [the task list](docs/TASKS.md), and consult the documents relevant to the change:

| Document | Purpose |
|---|---|
| [REQUIREMENTS.md](docs/REQUIREMENTS.md) | Acceptance requirements and their IDs: FR, VR, AR, DR, RR, and DEL |
| [ARCHITECTURE.md](docs/ARCHITECTURE.md) | Technical contracts, failure behavior, evaluation design, and implementation sequence |
| [PROPOSAL.md](docs/PROPOSAL.md) | Motivation, original scope, human responsibilities, risks, and fallback |
| [TASKS.md](docs/TASKS.md) | Work status and tasks designated human-only |
| [README.md](README.md) | User-facing setup, demo, evaluation instructions, and supported behavior |
| [AGENT_LOG.md](docs/AGENT_LOG.md) | Agent requests, changes, observations, and actual human decisions |
| [DEBUGGING.md](docs/DEBUGGING.md) | Real symptom, cause, fix, and verification evidence |
| [ATTRIBUTION.md](docs/ATTRIBUTION.md) | Reused sources/versions, licenses, and student/agent contributions |

The [course guide](https://www.cs.utexas.edu/~witchel/378AC/lectures/948_final_project_guide-slides.pdf) describes the course deliverables; Canvas supplies deadlines and submission logistics. Distinguish course requirements from additional project commitments. Do not silently remove a project commitment merely because the course guide does not require it.

## Required workflow for every change

1. **Identify the requirements.** State which requirement IDs the change implements, preserves, or affects, and what observable result would demonstrate success. Use the task summary or change description; a separate planning document is unnecessary.
2. **Inspect the starting state.** Check existing files and the working-tree diff. Preserve unrelated user work and distinguish working behavior from scaffolds. Choose a bounded change that advances the requested task.
3. **Preserve the contract.** Implement against the applicable requirements and architecture. Do not weaken acceptance criteria, remove checks, alter protected fixtures, or mark unfinished work complete to make a change appear successful.
4. **Check the result.** Run relevant available checks and inspect the diff. For behavior changes, use or add meaningful tests with independently determined expectations. For documentation-only changes, check consistency, links, and factual accuracy; application tests are unnecessary unless behavior is affected. If a check cannot run, record why and what remains unverified.
5. **Keep documentation consistent.** Update affected setup instructions, task status, design decisions, and evidence within the authorized scope. Record what changed and why when scope or requirements change. Distinguish planned, implemented, tested, and human-accepted work.
6. **Report evidence.** Summarize affected requirement IDs, changes, checks and actual outcomes, and remaining gaps. For significant claims, preserve inputs, commands, environment/configuration, expected results, actual results, and code references in the repository, subject to the human-owned records below (VR-3, RR-4).

Routine implementation choices within the agreed scope do not need repeated confirmation. If a material conflict cannot be resolved from the user's instructions and project documents, explain it and ask a focused question while continuing independent work. Apply already-authorized scope changes and reconcile affected documents; never silently choose whichever document makes a change easier.

## Scope and architecture boundaries

- `REQUIREMENTS.md` defines the acceptance contract. The current architecture extends the original CLIP-only plan with metadata filters, Tesseract OCR, BM25 text ranking, and reciprocal rank fusion. Earlier proposal/README/task descriptions have not all caught up. Preserve the original CLIP baseline and acceptance test while implementing explicitly requested hybrid work; document and reconcile relevant inconsistencies.
- Establish a working visual-search foundation before adding retrieval signals. Measure each addition separately. Captions, reranking, alternate encoders, SQLite, background workers, and approximate nearest-neighbor search are later experiments, not prerequisites for the demo.
- Use the planned local Python/Streamlit stack with Pillow, NumPy, PyTorch, and a pinned CLIP model/processor. Keep UI/CLI adapters thin and share production search logic with evaluation. Consult the architecture before introducing a dependency, service, or storage change.
- Preserve feature-to-photo associations, normalized finite vectors, deterministic tie-breaking, compatible feature signatures, and complete immutable index generations. Cache equality alone does not establish correctness. Apply metadata constraints before ranking; do not silently relax filters or disguise an unavailable OCR channel as no matching text.
- Keep the fixed-collection fallback runnable once implemented. Prioritize a complete, explainable workflow and evidence over additional features.

## Evaluation integrity and human ownership

- Respect tasks marked human-only in `TASKS.md` unless the user explicitly delegates that task. Do not invent student judgments, contribution claims, approvals, or acceptance decisions.
- Do not edit `eval/queries.json`, `eval/labels.json`, or frozen acceptance data. Do not inspect protected relevance labels, including through broad file dumps or search output. The human creates/freezes judgments and runs final acceptance. Use separate synthetic fixtures to implement and test evaluators without accessing protected labels.
- Retrieval/indexing must never consume relevance labels. Do not derive tags, filenames, captions, or OCR corrections from benchmark judgments to improve scores. Keep development experiments separate from held-out evaluation.
- The original metric is **mean precision@5**, not average precision/AP: count relevant distinct results in the first five, divide by five, then average over the original 15 queries. Current project targets are at least 0.60 and at least 0.20 absolute improvement over the filename baseline. These are targets, not measured results. Report actual outcomes even when targets are missed.
- Reject invalid evaluation inputs such as TODO queries, missing judgments, unknown image paths, repeated results, or missing benchmark images. Never silently omit difficult cases or change the denominator to improve results.
- Report original CLIP results separately from hybrid comparisons. Use the same corpus and eligible set when isolating a retrieval component's effect. Follow the architecture's development/held-out protocol and promotion criteria for hybrid mode.

## Validation and reproducibility

- Select checks relevant to the change: hand-calculated rankings, controlled feature-to-photo association fixtures, corrupt-image handling, cache reload/rebuild consistency, filter predicates, OCR failures, and interrupted index publication are examples described in the architecture.
- Tests must be capable of detecting plausible wrong behavior, not merely confirm that code executes. Preserve a regression check for a real defect when appropriate. Do not manufacture bugs or debugging stories to fill a documentation template.
- Verify dependency versions and model/processor revisions before recording them. OCR also needs an executable version and language-data checksum. Do not claim that the current placeholder `requirements.txt` provides a reproducible environment.
- Distinguish first-time setup/downloads from offline runtime. A prebuilt image index does not replace CLIP's text encoder assets. Validate offline behavior after dependencies and assets are prepared before claiming it works offline.
- Use documented commands only after confirming their entry points exist. At scaffold stage, commands such as `streamlit run app.py`, `python -m pytest`, and the architecture's CLI/evaluation commands are planned interfaces. Once implemented, keep actual commands and prerequisites in the README. Final evaluation using protected judgments remains human-run.
- Keep benchmark updates and failure experiments on temporary copies of frozen data. Preserve public-demo evidence in the repository; keep private photo collections and their derived artifacts local. Follow `.gitignore` and the project's explicit demo-artifact plan for generated files.

## Evidence, attribution, and completion

- For significant agent work, supply factual **request → change → observation → human decision** material. Use real diff/file references or existing commit IDs; do not invent commits or claim a human decision that has not occurred. Respect the task list's human ownership of final agent logs, debugging narratives, and contribution records; provide factual handoff notes and edit those records when explicitly delegated.
- Record reused source links, exact versions/revisions when verified, and applicable licenses. Distinguish student work, agent assistance, and reused components. Demo images are planned as verified CC0 sources with entries in `images/MANIFEST.csv`.
- Never introduce CS 378AC homework starter code, solutions, tests, data, or handouts into the repository or its history.
- Preserve the two decision comparisons in DR-1 and a real debugging episode in DR-2. If the user changes the decisions, update their rationale and evidence rather than silently discarding them.
- Do not treat a completed Markdown checklist as evidence that the application works. Final readiness includes runnable setup and demo/checks, actual results and limitations, attribution, and the approximately six-slide PDF. Staff access, Canvas submission, and demo scheduling require external confirmation; do not mark them complete based on local files.
