# Requirements Specification: Natural-Language Photo Search

**Course:** CS 378AC — Final Project
**Purpose:** Map the course requirements specification to concrete, verifiable commitments for this project. Each requirement is restated in project terms with how it will be satisfied and where the evidence lives.

---

## 1. Project Overview

### 1.1 Objective
Deliver a complete, verifiable user workflow for searching a local photo collection with natural-language queries, prioritizing a working end-to-end workflow and reproducible evidence over feature count.

### 1.2 Scope
- Completed individually.
- Scoped for ~two weeks of development and testing.
- Reuses pretrained CLIP and standard libraries; all reused components are attributed.
- **No** course homework material (starter code, solutions, tests, data, handouts) appears anywhere in the repo or its history.
- Student contributions, reused components, and AI-agent-generated work are distinguished (see `ATTRIBUTION.md`, planned).

---

## 2. Functional Requirements

### FR-1: Core Functionality
One complete working workflow: **import folder → index images → text query → ranked thumbnail results → open original.**

| Property | Commitment |
|---|---|
| User / use case | Owner of a photo library with uninformative filenames. |
| Reproducible initial state | Committed CC0 image set + manifest; empty or pre-built index. |
| Defined inputs | A folder of images; a free-text query string. |
| Central functionality | CLIP embedding + cosine-similarity ranking. |
| Observable result | Ranked thumbnail gallery; click opens the original. |

**Consequential claim:** CLIP-based semantic search achieves **average precision@5 ≥ 0.60** on 15 pre-labeled queries, beating a filename-overlap baseline by **≥ 0.20 absolute**.

### FR-2: Demonstrable Behavior
Reproducible demo covering:
- **Success:** search several queries, open relevant results.
- **Observable state change:** build index, then restart to show the cached index is reused.
- **Edge / failure / invariant:** corrupt image file handled without crashing; restart consistency (same query → same results).
- **Limitation:** a difficult query (counting / spatial relationship) or a query with no relevant photos.

Demo runs **locally without network access** (model pre-cached). It does not depend exclusively on unpredictable external services.

### FR-3: Defined Scope and Limitations

| Category | Items |
|---|---|
| **Core** | Folder import, embedding + persistent index, text query, ranked thumbnails, open original. |
| **Optional** | Live folder updates, search-by-image, similarity threshold tuning UI. |
| **Known limitations** | Counting, fine spatial relations, OCR/text-in-image, face identity; very large collections (brute-force similarity). |

If the original scope becomes infeasible, the fallback (one fixed pre-indexed collection with text search + gallery) is preserved and the change is documented.

---

## 3. Testing and Validation Requirements

### VR-1: Functional Testing
Tests verify observable behavior, not merely that code runs:
- **Primary workflow:** precision@5 over 15 labeled queries meets the target.
- **Edge / failure:** corrupt / unreadable image is skipped and reported; empty query handled.
- **Mutation sensitivity:** a plausible incorrect implementation (e.g., returning results in arbitrary / insertion order instead of by similarity, or mismatching embeddings to filenames) is detectable — the precision@5 and restart-consistency checks would fail.

### VR-2: Independent Validation
At least one check with an implementation-independent expected result:
- **Labeled ground truth:** 15 queries with human relevance labels committed **before** tuning; agents may not modify them.
- **Differential comparison:** filename-overlap baseline computed independently of the CLIP path.
- **Invariant:** index rebuilt from the same images yields the same rankings; a reloaded cached index matches a freshly computed one.

### VR-3: Reproducibility
For each significant claim the repo records: the input / starting state, the command to run, the expected result and how it was determined, the actual observed result, and the relevant code/test/commit. Testing instructions and artifacts (labels, manifest, scripts, recorded outputs) are retained in the repo.

---

## 4. AI-Agent Usage and Human Oversight

### AR-1: Human Responsibility
I remain responsible for design, correctness, and quality. Consequential decisions (scope, architecture, model choice, validation, labels) are documented.

### AR-2: Agent Task Documentation
At least **two traceable agent-work episodes** are preserved in `docs/AGENT_LOG.md` (planned), each capturing **Request → Change (diff/commit) → Observation (tests/measurements) → Human decision (accept/correct/reject/redesign)**, including at least one consequential human correction, rejection, or redesign.

### AR-3: Independent Acceptance
No agent change is accepted solely on the agent's report. Important changes are inspected, executed, and validated against the labeled queries and controlled image sets.

---

## 5. Technical Decision Requirements

### DR-1: Two Design or Evaluation Decisions
Each documented with problem, chosen solution, an alternative considered, associated implementation changes, evidence used, and what was retained/changed/rejected.
1. **Semantic (CLIP) vs. filename search** — evaluated via precision@5 on labeled queries.
2. **Persistent embedding cache vs. recompute on startup** — evaluated via indexing time and startup latency measurements.

### DR-2: Debugging Case Study
At least one substantive story with **Symptom → Cause → Fix → Verification**, retained in `docs/DEBUGGING.md` (planned). May overlap with a DR-1 decision.

---

## 6. Repository and Documentation Requirements

- **RR-1 GitHub repository:** single repo under my account; instructor/TA access granted before submission; no homework material in history; URL submitted via Canvas.
- **RR-2 Setup documentation:** `README.md` (planned) covers dependencies/environment, install/build, how to start and interact, how to reproduce the demo, how to run validation checks, and required test data/fixtures/config.
- **RR-3 Attribution and contributions:** `ATTRIBUTION.md` (planned) lists external libraries/frameworks, reused versions/commits (incl. CLIP model revision), student-added functionality, and significant AI-agent contributions.
- **RR-4 Evidence preservation:** presentation claims trace to commits, diffs, test scripts, fixtures, recorded outputs, or measurements.

---

## 7. Proposal
See `docs/PROPOSAL.md` (addresses PR-1 through PR-4).

---

## 8. Final Deliverables
- **DEL-1 Working project:** runnable implementation with setup instructions and evidence.
- **DEL-2 Live demonstration:** known initial state → primary workflow → observable result → edge/failure → one independently established expected result → two decisions → reused components + a limitation.
- **DEL-3 Final presentation:** ~6-slide PDF (Overview, Contributions, Decision 1, Decision 2, Debugging Story, Limitations).

---

## 9. Suggested Implementation Milestones

| Milestone | Required outcome |
|---|---|
| **E0 — Contract** | Finalize core claim, reproducible setup, initial acceptance check (labels + baseline committed). |
| **E1 — Behavior** | Implement core workflow; demonstrate a meaningful edge case or invariant. |
| **E2 — Control** | Document agent-assisted changes, human review, and consequential corrections. |
| **E3 — Defense** | Complete independent validation, reproducible demo instructions, known limitations. |

Development checkpoints only; actual due dates come from Canvas.

---

## 10. Submission Checklist
- [ ] Instructor and TA can access the repository.
- [ ] Project runs from a clean setup.
- [ ] Documented validation inputs, commands, and results present.
- [ ] Repository URL and final slide deck PDF submitted together.
- [ ] Demonstration slot reserved via Canvas.
- [ ] All changes complete before the available-until deadline.

---

## 11. Final Acceptance Criteria
- [ ] Clearly defined user and complete core workflow.
- [ ] Core workflow runs from a reproducible initial state.
- [ ] System produces an observable, meaningful result.
- [ ] At least one test can identify a plausible implementation defect.
- [ ] At least one expected result is established independently.
- [ ] A meaningful edge case or failure is demonstrated.
- [ ] A specific limitation is documented.
- [ ] Two consequential design/evaluation decisions explained with evidence.
- [ ] One debugging story with cause, correction, and verification.
- [ ] Reused work, student contributions, and AI-agent assistance attributed.
- [ ] Setup and test instructions work from a clean checkout.
- [ ] Repository accessible to course staff.
- [ ] Final slide deck and repository URL submitted.
- [ ] Demonstration slot scheduled.

> **Guiding principle:** demonstrate one complete, meaningful capability with strong, reproducible evidence. Additional features are secondary to correctness, validation, and the ability to explain and defend the work.
