# monday.com board schema — "CGN Brain Issues- testE2M"

## Board creation (first run)

On first-ever sweep, check if a board named "CGN Brain Issues- testE2M" exists in the CGN workspace. If not, create it with the columns below.

## Columns

| Column name | Type | Notes |
|---|---|---|
| Name | (default text) | "[Category code] Short summary — client_id" |
| Category | Status / dropdown | Options: A1..F3 (from taxonomy.md) |
| Severity | Status | High / Medium / Low |
| Client | Text | Client ID affected, or "n/a" |
| Reporter | People | Whoever's sweep first surfaced it |
| First Seen | Date | Timestamp of first detection |
| Last Seen | Date | Updated on each sweep that re-affirms the issue |
| Occurrence Count | Numeric | Incremented each sweep |
| Evidence | Long text | Slack permalink(s), session ID(s), quote excerpts |
| Status | Status | New / Triaged / In Progress / Resolved / Won't Fix |
| Owner | People | Assigned after triage |
| Detection source | Status | Active probe / Slack scan / Transcript scan / Canary / Ad-hoc |

## Dedup key (stored in an item hidden field or tag)

A string built from `(category_code + "::" + client_id + "::" + normalized_summary_hash)` so we can match incoming detections against existing items without fuzzy comparison.

## Board metadata (stored in a "Meta" group or board description)

- `last_canary_run`: ISO date of the most recent canary query run
- `canary_rotation_index`: which client the per-client stats probe (P3) should start with next run
- `schema_version`: this plugin's version

## Initial groups

- **New** — everything filed but not yet triaged
- **In Progress** — being worked on
- **Resolved** — closed, kept for 30 days then archived
- **Parking Lot** — filed but deferred (won't fix for now)