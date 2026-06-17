# Skill Library Taxonomy

A reusable "skill" is a documented, self-contained capability an agent can load on demand — a procedure plus the context to execute it. Once a library passes ~50 skills, *findability* becomes the bottleneck. This is the taxonomy that keeps a 100+ skill library navigable.

---

## Structure

```
{Category}/{skill-name}/{skill-name}.md
```

- One skill per folder; the entry file is named for the skill (not a generic `SKILL.md`) so it's identifiable in any file list or search.
- Categories group by *domain of work*, not by tool.
- A single use-case lookup index maps "I need to do X" → the exact skill, so discovery is a lookup, not a scan.

## Authoring rules

- **Description = one sentence, active verb, what it does.** Trigger/when-to-use language lives in the loader, not the description.
  - ✅ "Score deals by probability-to-close using condition, comps, and financials."
  - ❌ "Use when you need to..."
- **Keep entry files focused.** Long references split into a `references/` subfolder loaded only when needed.
- **Register on creation.** A new skill is added to the category folder, the master index, and the use-case lookup in the same change — or it's effectively invisible.

## External-skill tracking

Skills installed from third parties are tracked in a dedicated table with: name, install date, source URL, and purpose. This lets an agent check upstream for updates and keeps provenance clear.

## Lifecycle

- **Usage audit** — periodically scan session transcripts for actual skill invocations (real tool calls, not just name mentions) across *all* projects, not one folder. Skills with genuine zero usage are archived, not deleted.
- **Consolidation** — overlapping single-purpose skills are periodically merged into bundles to reduce load cost and decision overhead.

> A subtle failure mode: scanning only one project's history makes heavily-used skills look unused. Always audit the full history before pruning.
