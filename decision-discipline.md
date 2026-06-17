# Decision Discipline

Every change to system config, tooling, hooks, or process is logged ADR-style (Architecture Decision Record) in a running decisions log. The discipline matters more than the format: nothing changes silently, and every constraint can be traced to the decision that created it.

---

## The rule

- **Before** any change to settings, hooks, scheduled jobs, or scripts → read the decisions log first for active constraints.
- **After** any change → log it the same day: what changed, why, and the constraint it introduced.
- When asked about a past change → the log is the source of truth, not memory.

## Entry format

```
## YYYY-MM-DD — <short title>
Change: <what changed, concretely>
Why: <the reason / problem it solved>
Constraint created: <what this now requires or forbids going forward>
```

## Why this pays off

- **No re-litigating settled decisions.** The log records *why* a choice was made, so it isn't second-guessed every session.
- **Rejected options are preserved.** Recording what was *passed on* — and why — is as valuable as recording what was chosen; it stops the team from re-exploring dead ends.
- **Constraints are explicit.** A guardrail with a dated rationale ("written after X cost us Y") is followed; an unexplained rule is worked around.
- **Onboarding is reading, not archaeology.** A new collaborator (or a fresh agent session) reads the log and inherits the full decision context.

## Example shape (sanitized)

```
## 2026-0X-XX — File-system hard separation
Change: split live project code from tooling/config into separate roots.
Why: skills and scripts were leaking into project folders, causing duplicate/
     conflicting config and confused builds.
Constraint created: skills run *against* projects, never live inside them.
```

This is the same instinct behind a good monorepo CHANGELOG or an RFC archive — applied to the *operating environment*, not just the code.
