
---
name: qa-sweep
description: Scans the user's Slack, session transcripts, and the CGN client brain itself for brain-related issues (auth failures, duplicate clients, factual inaccuracies, conflicts, retrieval gaps, etc.) and aggregates findings to a central Slack channel and monday board. Use when the user says "run cgn brain qa", "sweep the cgn brain", "check cgn brain health", "cgn brain audit", "run a brain sweep", or when invoked automatically by the scheduled-tasks MCP on its 2-day cadence.
---

# CGN Brain QA Sweep

You are running a structured QA pass over the CGN client brain to surface issues the team should fix. Each teammate has this plugin installed; you run in their environment and log findings to a shared destination so E2M and CGN leadership can triage.

## Destinations (fixed)

- **Slack:** `#e2m-and-cgn` (channel ID `C0ADRFD1S59`)
- **monday board:** "CGN Brain Issues- testE2M" (board ID `18413317001`) — if it doesn't exist yet, create it on first run using the schema in `references/monday-board-schema.md`

## Preflight (run on every invocation)

1. **Check MCPs are connected.** The required servers are listed in `references/required-mcps.md`. If any are missing, report to the user which are missing and stop — don't fail silently.
2. **Check auth to the CGN brain.** Call `auth_status` on the CGN brain MCP. If unauthenticated, log this as an **A1** issue, skip the probes, and still run the passive Slack/transcript scans.
3. **On first-ever run**, call the scheduling setup in `references/scheduling.md` so this runs every 2 days going forward.

## The Sweep

Run these in order. Each detection step maps to category codes in `references/taxonomy.md` — use those exact codes when filing issues.

### Step 1 — Active probes

Read `references/active-probes.md` and run every probe. Each probe is a specific MCP call with an expected-vs-actual check. Probes cover categories **A** and **B**.

### Step 2 — Passive Slack scan

Read `references/slack-patterns.md` for the keyword set and channel scope. Search the channels listed there over the last **N days** (default 7 on normal runs, 30 on the first run of a new installer). Focus on **C1, F2, F3**.

**Scope:** exactly these 10 internal CGN channels — `#general`, `#internal-marketing`, `#ai-tools`, `#sales`, `#client-success`, `#seo`, `#webdev`, `#tide-pod`, `#naskr-pod`, `#e2m-and-cgn`. **Do NOT scan DMs**, client-specific channels, or notification/log channels. `#e2m-and-cgn` is both scanned AND used as the digest destination. If new internal channels are created, add them to `references/slack-patterns.md`.

### Step 3 — Session transcript scan

Read `references/transcript-patterns.md`. Enumerate recent sessions via `session_info.list_sessions`, then read transcripts looking for:

- Tool errors from the CGN brain MCP → **A5**
- User frustration language near brain responses → **C1, E1**
- **Brain self-flagged conflicts in assistant responses** → **C2** (highest-value signal — the brain already calls these out in conversation; harvest them)
- `[UNKNOWN - NEEDS CLIENT INPUT]` tokens in generated docs → **D1**

### Step 4 — Canary queries (optional, run once per week max)

Read `references/canary-queries.md`. Run a small set of known-answer questions per client and compare against expected answers. Catches **C3, C6, C7, E3**. Skip this step if the last canary run was within 7 days — find the last run timestamp in the monday board metadata.

### Step 5 — Active SEO clients roster diff (monday Client Database)

Source of truth for "active SEO client" is the monday **Client Database** board (board ID `18245097205`), group **Active Clients** (group id `topics`), filtered to items whose **Services Purchased** dropdown contains "SEO".

Diff that list against `list_clients` from the CGN brain, applying the rules in `references/active-probes.md` (probe **P5**) and the explicit override mappings in `references/client-id-mappings.md`. Flag:

- In Active SEO list, no fuzzy/override match in brain → **B4** (missing client)
- In Active SEO list, matched to a brain ID with 0 docs → **B6** (active SEO client awaiting ingestion)
- Brain ID with docs, no match in Active SEO list → **B3** (likely churned — or non-SEO active; requires triage)
- Brain ID matches an Active SEO client but the names diverge (rename) → **B7** (stale client ID — silently breaks queries by current name)

HubSpot is no longer the source of truth for this diff and should not be used.

### Step 6 — Manual item triage

Read `references/manual-item-triage.md`. Scan the CGN Brain Issues- testE2M board for top-level items added by teammates outside the sweep (no Category, Severity, Owner, or Dedup Key set) and route each one into the proper category parent + subitem structure. Each manual report gets a real category code, severity, owner, dedup key, and is filed under (or as) the appropriate parent — then the loose top-level item is deleted.

This closes the loop on ad-hoc reports so E2M/CGN don't have to interpret loose titles. Tag detection_source = `Ad-hoc` so the team can tell auto-triaged manual reports from automated detections.

## Filing Findings

For every detected issue, follow the dedup logic in `references/dedup-logic.md` before creating a new monday item. The short version: build a dedup key `(category_code, client_id, normalized_summary)`, query the monday board for open items matching that key, and either create a new item or append an "occurrence" update to the existing one.

Use the field schema in `references/monday-board-schema.md` for every new item. Group related occurrences as **subitems** under a single parent (issue type), not as flat items — see the schema doc for the parent/subitem pattern.

## Digest to Slack

After the sweep completes, post a single threaded digest to `#e2m-and-cgn` following the template in `references/slack-digest-format.md`. Include:

- System-status line (✅/⚠️/❌ + one-line summary)
- New issues this run (count + brief list with links to monday items)
- **Auto-triaged manual reports (Step 6)** — count + brief list of `original title → reclassified as [code] under {parent}`
- Re-affirmed open issues (count only, not each one)
- Resolved-since-last-run (if any moved to Done in monday)

**Do not post the digest if the sweep found nothing new and nothing changed.** Silence is fine — the goal is signal, not noise.

## Severity rules

Use the severity heuristic in `references/taxonomy.md`:

- **High**: A1, A2, A5, B3, B4, B6, B7, C1, C2, C3, C5, C6
- **Medium**: A3, A4, A6, B1, B2, B5, C4, C7, D1, D2, D3, E1, E2, E3
- **Low**: F1, F2, F3

## Reporting back to the user in-session

After the sweep, summarize to the user in plain language: what you checked, what you found, and a link to the monday board. Keep it short — the full detail lives in monday. If this was a scheduled-task invocation (no interactive user), skip the in-session summary and just post the Slack digest.