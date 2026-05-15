# Active probes

Run these in order on every sweep. Each probe targets specific taxonomy codes.

## P1 — Auth status
**Tool:** `cgn-brain.auth_status`
**Check:** `authenticated === true` and `token_present === true`.
**On failure:** File **A1**. Include `login_url` from the response in the evidence field so teammates can fix it themselves.

## P2 — Client list integrity
**Tool:** `cgn-brain.list_clients`
**Expected:** ≥ 49 clients (current baseline per Patrick's 4/24 message). Zero-doc count should be ≤ 2 (the two legitimate shells that existed at the time of this plugin's creation).
**Checks:**
- If the list is empty and P1 passed → **A2**
- For each entry, flag as **B1** if all true: `document_count === 0` AND the ID is within Levenshtein distance 3 of another ID in the list with `document_count > 0`. Known current duplicates: `charleene`, `charleen_s_houses`, `charleenes_housing`, `charleene_s_houses`, `frei_remodelling`, `frei_remodeling`, `renaissance-building`.
- For each entry with `document_count === 0` that is NOT a duplicate → preliminary **B2** (refined by P5 — if it matches an Active SEO client, it becomes **B6** instead)

## P3 — Per-client stats spot check
**Tool:** `cgn-brain.get_client_stats` for a rotating sample of 5 clients per run (never all 49 — too slow).
**Selection:** round-robin via a counter stored in the monday board's metadata. This way over ~10 runs, every client gets checked.
**Checks:**
- If `last_ingested_at` is > 30 days old for a client with an active Drive folder → **B5**
- If `chunk_count` is significantly below the mean for clients with comparable document counts → flag for human review

## P4 — Canary query (weekly, not every run)
See `canary-queries.md`. Skip if last canary run was < 7 days ago.

## P5 — Active SEO clients roster diff (monday Client Database)

The canonical "active SEO client" list lives in the **Client Database** monday board.

**Source of truth:**
- Board ID: `18245097205`
- Group: **Active Clients** (group id `topics`)
- Filter: **Services Purchased** dropdown column (`dropdown_mkwzpqd9`) contains the substring `SEO`

This catches every SEO tier: SEO Build, SEO Foundation, SEO Expand, SEO Small Market, SEO Mid-Market, SEO Large Market, 18 Month SEO Deal, etc.

**Tool calls:**
1. `monday.get_board_items_page(board_id=18245097205, filters=[{group=topics, services contains "seo"}], columns=[name, status, dropdown_mkwzpqd9])` → set **A** (active SEO clients)
2. `cgn-brain.list_clients` → set **B** (brain roster, with `document_count`)

**Matching rules (in order):**
1. **Explicit override:** consult `references/client-id-mappings.md` for any explicit `monday_name → brain_id` rows. If a row matches, that's the authoritative mapping — skip fuzzy match.
2. **Normalized fuzzy match:** lowercase both, replace `_` with `-`, strip non-alphanumerics except `-`, drop common prefixes/suffixes (`llc`, `inc`, `the`, `&`, `pod-transfer`, parenthesized notes). Levenshtein distance ≤ 3 considered a match.
3. **No match:** treat as B4/B3 candidate per the diff rules below.

**Diff rules:**
- A∖B (in Active SEO list, no match in brain) → **B4** missing
- A ∩ B but brain has 0 docs → **B6** active SEO client awaiting ingestion
- A ∩ B but normalized brain ID name diverges from monday name → **B7** stale client ID (silent failure on queries by current name)
- B∖A where brain has > 0 docs → **B3** orphan candidate (likely churned; needs triage to confirm not non-SEO active)
- B∖A where brain has 0 docs AND no fuzzy match in A → confirms **B2** zero-doc not-active

**On filing:**
- Group findings by category as parent items, with one subitem per affected client. Don't file 30 flat items — see `monday-board-schema.md` for the parent/subitem convention.
- New B7 detections should be added to `client-id-mappings.md` once Logan/E2M confirms the rename, so subsequent runs treat the mapping as authoritative.

**If monday MCP isn't connected:** skip P5 and log a note in the digest that the active-SEO diff couldn't be checked. Do not fall back to HubSpot — HubSpot's `lifecyclestage = customer` is too broad (it returned 293 historical records on test) and is no longer used for this diff.