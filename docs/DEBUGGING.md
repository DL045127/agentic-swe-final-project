# Debugging Case Study

Satisfies **DR-2**. Records at least one substantive debugging story. May overlap with a DR-1 decision.

Each story captures:
- **Symptom** — what was not working correctly (observable).
- **Cause** — how the underlying issue was identified.
- **Fix** — the implementation change that resolved it.
- **Verification** — the test or observation confirming the fix.

> **Status:** Scaffold. Replace with a real episode once a non-trivial bug is found and fixed. A likely candidate given the design: embedding rows becoming misaligned with the manifest (row *i* ↔ image *i* invariant), surfacing as correct-looking results pointing at the wrong photos.

---

## Story 1 — <short title>

- **Symptom:** `TODO — e.g., after rebuilding the index, thumbnails no longer matched their scores.`
- **Cause:** `TODO — how it was isolated (logging, a failing invariant check, a minimal repro).`
- **Fix:** `TODO — the code change (commit <hash>).`
- **Verification:** `TODO — the test/observation that confirmed it (e.g., restart-consistency check now passes; precision@5 restored).`
