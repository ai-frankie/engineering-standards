# Workflow Automation — n8n Lead-Enrichment Pipeline

Four production [n8n](https://n8n.io) workflows that run a contact-enrichment pipeline end to end: ingest a lead list from a spreadsheet, enrich it against a third-party data API, deduplicate and upsert the results, and generate a daily call sheet — on a schedule, idempotently.

These are **genericized exports** of automation I built and ran for a real client engagement. All client identifiers and the source spreadsheet ID have been replaced with placeholders; credentials resolve from n8n's credential store and environment variables (`{{ $env.HUNTER_API_KEY }}`), never from the file.

> **Why this is here:** the rest of this repo documents *how* I run engineering with rigor. These workflows are that rigor applied to real ops automation — idempotent writes so retries never duplicate, batched API calls that respect rate limits, scheduled refresh with a normalize-then-upsert step, and a clean separation between ingest, enrich, and report.

---

## The pipeline

```
Source CSV import ──▶ Normalize ──▶ Upsert to Contacts sheet   (weekly-refresh, Sun 3AM cron)
                                          │
Contacts sheet ──▶ Filter (Tier 1/2,    │
                   no email yet) ──▶ Extract domain ──▶ Hunter.io
                   domain search ──▶ Score & pick best contact ──▶
                   Write enriched row back   (contact-enrichment)
                                          │
Contacts sheet ──▶ Format ──▶ Daily call sheet tab   (daily-callsheet)
```

## The workflows

| File | Trigger | What it does | Notable pattern |
|---|---|---|---|
| `weekly-refresh.json` | Cron (Sun 3 AM) + manual | Reads a source CSV import, normalizes rows, **upserts** into the contacts sheet | Idempotent upsert — re-runs never duplicate rows |
| `contact-enrichment.json` | Manual | Filters un-enriched contacts, extracts company domains, calls Hunter.io, scores and writes back the best match | API enrichment with a scoring step, not blind first-result |
| `contact-enrichment-batched.json` | Manual | Same enrichment, but **one-at-a-time batched** with skip/no-op branches | Rate-limit-safe batching via `splitInBatches` + loop-back |
| `daily-callsheet.json` | Manual | Reads contacts, formats a prioritized call list, clears and rewrites the call-sheet tab | Clean rebuild — clear-then-write avoids stale rows |

## Design notes

- **Idempotency over append** — every write path upserts or clears-then-writes, so a re-run is safe. No "did this already run today?" guesswork.
- **Batched, rate-limit-aware enrichment** — the batched variant processes one record per Hunter call with explicit skip branches, so a partial run is resumable and the API isn't hammered.
- **Scoring, not first-match** — enrichment picks the best contact by seniority/department signal rather than taking whatever the API returns first.
- **Secrets stay out of the file** — Google Sheets auth is an n8n credential reference; the Hunter API key is an env var. The JSON is safe to publish.

## Run it yourself

1. Import any `.json` into n8n (**Workflows → Import from File**).
2. Create a **Google Sheets** credential and select it on the sheet nodes.
3. Set `HUNTER_API_KEY` in your n8n environment.
4. Replace `YOUR_GOOGLE_SHEET_ID` with your own spreadsheet ID.
5. Run manually, or enable the cron trigger on `weekly-refresh`.
