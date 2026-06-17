# Agent Coordination

Patterns for running coordinated teams of named agents. The core idea: agents coordinate by **messaging each other directly**, not by polling shared state. A lead spawns the team, each agent knows who to hand off to, and the work flows without a central bottleneck.

---

## Topology patterns

| Pattern | Flow | Use when |
|---|---|---|
| **Pipeline** | A → B → C → D | Sequential dependencies (e.g. research → design → build → test) |
| **Fan-out** | Lead → A, B, C → Lead | Independent parallel work (e.g. multi-area research) |
| **Supervisor** | Lead ↔ workers | Ongoing coordination (e.g. complex refactor) |

## Rules

- **Always name agents.** A name makes an agent addressable for handoff.
- **Embed comms in the prompt.** Each agent is told who to message and what to send — coordination is part of the task, not an afterthought.
- **Spawn the whole team in one shot**, then stop and report what's running. Don't poll — agents message back or complete on their own.
- **Cap the fan-out.** Parallel agent count has a hard limit (see [guardrails.md](guardrails.md)); exceeding it requires explicit confirmation.

## When to coordinate vs. go solo

**Coordinate** for: 3+ files, new features, cross-module refactors, API changes, security/performance work.

**Go solo** for: single-file edits, 1–2 line fixes, doc updates, config changes, questions.

> Coordination has overhead. A two-line fix doesn't need a swarm; a cross-cutting feature does. Matching the topology to the task size is itself a guardrail against wasted cost.

## Pipeline sketch

```
Lead spawns, in one message:
  researcher  → "research the codebase, hand findings to architect"
  architect   → "wait for researcher, design solution, hand to coder"
  coder       → "wait for architect, implement, hand to tester"
  tester      → "wait for coder, write tests, report to reviewer"
  reviewer    → "wait for tester, review quality + security"

Lead then kicks off the pipeline and waits.
```
