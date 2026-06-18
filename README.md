# Engineering Standards — AI-Orchestrated Development Practice

How I run a high-velocity engineering practice with AI agents *without* sacrificing rigor. This repo documents the guardrails, decision discipline, and reusable patterns I apply across every project — a deterministic operating system for AI-assisted software work.

> **Thesis:** AI raises velocity; the risk is that it also raises entropy — runaway cost, hallucinated facts, scope creep, leaked data. These standards exist to keep velocity *and* control. Treat the AI as a force multiplier governed by hard limits, not a black box.

---

## Contents

- **[guardrails.md](guardrails.md)** — cost-conservation, batch/loop limits, sub-agent caps, and the simplicity/surgical-change principles that keep AI output disciplined.
- **[decision-discipline.md](decision-discipline.md)** — how every system change is logged ADR-style, with what changed, why, and the constraint it created.
- **[skill-library.md](skill-library.md)** — taxonomy for organizing a 100+ reusable-skill library so the right capability is found in seconds, not rebuilt.
- **[agent-coordination.md](agent-coordination.md)** — SendMessage-first multi-agent patterns (pipeline, fan-out, supervisor) for coordinated teams of named agents.
- **[workflows/](workflows/)** — four production n8n automation workflows (genericized): a scheduled, idempotent contact-enrichment pipeline (Google Sheets ETL + Hunter.io API + dedup/upsert). The discipline above, applied to real ops automation.

## Operating principles (the short version)

1. **Token/cost conservation is non-negotiable.** Route work to the cheapest tool that can do it; reserve the expensive reasoning model for what only it can do.
2. **Hard limits, not soft guidance.** Batch runs, agent fan-out, and retry loops have explicit numeric caps that require confirmation to exceed. (This rule was written *after* an uncapped batch run cost real money — see decision-discipline.)
3. **Simplicity first.** Minimum code that solves the problem. If 200 lines could be 50, rewrite it.
4. **Surgical changes.** Every changed line traces to the request. No drive-by refactors.
5. **Goal-driven execution.** Define testable success criteria before coding, then loop until verified.
6. **Verify before declaring done.** Completion requires evidence, not assertion.

---

*This is a sanitized, public extract of my private engineering-operations config. Project-specific paths, credentials, and personal data are excluded by design.*
