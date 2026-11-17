# Agent-Work Log

Satisfies **AR-2** and feeds **AR-3**. Preserves traceable agent-work episodes. The spec requires **at least two** episodes, including **at least one** consequential human correction, rejection, or redesign.

Each episode captures:
- **Request** — the specific, bounded task given to the agent.
- **Change** — the resulting diff or commit (link/hash).
- **Observation** — tests, measurements, or manual checks performed.
- **Human decision** — accepted / corrected / rejected / redesigned, and why.

> **Status:** Scaffold. Replace the templates below with real episodes as work is delegated. Keep the commit hashes accurate so claims trace to artifacts (RR-4).

---

## Episode 1 — <short title>

- **Date:** `TODO`
- **Request:** `TODO — e.g., "Implement indexer.py: walk a folder, skip corrupt images, batch through the encoder."`
- **Change:** `TODO — commit <hash> / PR link`
- **Observation:** `TODO — e.g., ran eval/evaluate.py; corrupt-file test; manual spot check of 3 queries.`
- **Human decision:** `TODO — accepted / corrected / rejected / redesigned, with reason.`

---

## Episode 2 — <short title>  *(include a consequential correction/rejection/redesign)*

- **Date:** `TODO`
- **Request:** `TODO`
- **Change:** `TODO — commit <hash>`
- **Observation:** `TODO`
- **Human decision:** `TODO — this is a good place to document a rejection or redesign and what the agent got wrong.`

---

## Notes

- AI-generated code is **not** accepted solely because the agent reports success (AR-3); each important change is independently inspected, executed, or validated.
- Evaluation relevance labels (`eval/labels.json`) are **off-limits to agents** to preserve an independent basis for judgment.
