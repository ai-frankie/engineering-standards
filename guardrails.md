# Guardrails

Hard limits and principles that govern AI-assisted work. These are enforced, not aspirational — several were written in response to a specific failure.

---

## 1. Cost / token conservation

The expensive reasoning model is the scarcest resource. Spend it only on what nothing else can do.

| Job | Route to | Reasoning model's role |
|---|---|---|
| Content, copy, summaries, narratives | Document/notebook tooling | Prompt it, review output |
| Research, web lookup, docs | Search/retrieval tooling | Prompt it, verify sources |
| Code, scripts, config | Reasoning model | Write it |
| Architecture, QA, decisions | Reasoning model | Think it |

**Rule:** before issuing a prompt, ask *"is this the reasoning model's job?"* If it's content or research, route it to a cheaper tool first.

## 2. Batch / loop guard — required before any bulk run

Before running a skill or operation in bulk:

1. State the item count and expected operation count (items × ops-per-item).
2. Estimate the token cost (calls × tokens × price).
3. Wait for explicit approval before proceeding.
4. For large batches, pre-create an approval flag so the run is auditable.

> **Why this exists:** an uncapped bulk run once processed dozens of items in a single session and produced a large, avoidable bill. The guard turns an implicit loop into an explicit, approved, costed decision.

## 3. Sub-agent cap — hard limit

- Never spawn more than **5 sub-agents in parallel** without explicit "yes, run all N" confirmation.
- Never loop over more than **5 items** in a single session without confirmation.
- Always state exactly how many agents will spawn and what each does *before* dispatching.

## 4. Autonomous-run restriction

- Never run an autonomous retry loop without an explicit requirements file as input.
- Always confirm the max retry count before starting; default low, raise only on request.

## 5. Tooling moratorium

- No new tools/plugins/services during a moratorium window. If something looks useful, log it to the backlog and revisit on the review date.
- Rationale: a working stack compounds. Churn resets the compounding.

---

## Code-quality principles

### Simplicity first
Minimum code that solves the problem. Nothing speculative. No abstractions for single-use code, no configurability that wasn't requested, no error handling for impossible scenarios. Test: *"would a senior engineer call this overcomplicated?"* If yes, simplify.

### Surgical changes
Touch only what the request requires. Don't refactor working code, don't reformat adjacent lines, match existing style. Every changed line must trace directly to the request. Note unrelated dead code — don't delete it silently.

### Think before coding
State assumptions explicitly before implementing. If multiple interpretations exist, present them — don't pick silently. If something is unclear, stop and ask.

### Goal-driven execution
Transform vague tasks into testable goals before writing code:
- "Add validation" → "write tests for invalid inputs, then make them pass"
- "Fix the bug" → "write a test that reproduces it, then make it pass"
- "Refactor X" → "ensure tests pass before and after"

Strong success criteria let the agent loop independently; weak criteria ("make it work") force constant clarification.

### Verify before completion
Done means verified. If tests fail, say so with the output. If a step was skipped, say that. State completion plainly only when there's evidence behind it.
