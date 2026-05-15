# Manual item triage

Teammates can drop new findings directly onto the CGN Brain Issues- testE2M board without going through the sweep — type a quick title in monday and hit save. These start uncategorized (no category code, severity, owner). This step catches those and routes each one into the proper place in the taxonomy so E2M and CGN can act on them like any other finding.

## When to run

Step 6 of the sweep — after the active SEO clients diff (Step 5), before posting the Slack digest. Manual items get the same triage treatment as automated detections so the board stays consistent and nothing falls through the cracks.

## Detection — what counts as a "manual" item

Query the CGN Brain Issues- testE2M board (`18413317001`) for top-level items (not subitems) where ANY of these hold:

- Category column (`color_mm2t4pj0`) is empty
- Severity column (`color_mm2tfa51`) is empty
- Action Owner column (`color_mm2tgy6d`) is empty
- Dedup Key column (`text_mm2t131`) is empty

These are candidates for auto-triage.

**Skip** any item that:

- Is a subitem (it was filed correctly under a parent)
- Already has Category + Severity + Owner all filled in (sweep-filed or already triaged)
- Is in Triage Status = `Resolved` or `Won't Fix` (closed)

## Triage rules

For each candidate item:

1. **Read** the Name, Long Text body, and any Updates/comments on the item.
2. **Identify the affected client(s)** from the text:
   - Look for any `client_id` from `list_clients`
   - Look for the client's display name as it appears in the Active SEO clients board (board `18245097205`)
   - Apply the override mappings in `client-id-mappings.md` (e.g., "Lifestyle Design + Remodel" → `apex-home-innovations`)
   - "Tribbles" / "Tribble" → `tribble-painting`, etc. — common-sense fuzzy matching is fine
3. **Pick the best category code** using `taxonomy.md`. Prefer specificity. Common patterns:

| User language | Likely code |
|---|---|
| "Brain didn't have X service info" / "missing service in brain" | **D2** (uneven category coverage) |
| "Generated content was wrong because brain was missing X" | **C1** (factual error) |
| "Brain made up content / hallucinated X" | **C5** (hallucination) |
| "Brain returned client A's info when I asked about client B" | **C6** (cross-client bleed) |
| "Couldn't find X for [client]" / "no results" / "empty response" | **E1** (low-confidence for known content) |
| "Brain query timed out" / "took forever" | **F1** (slow / timeout) |
| "Brain has the old name for [client]" | **B7** (stale client ID) |
| "Client X is missing from the brain" | **B4** (missing client) |
| "Brain has client X but we churned them months ago" | **B3** (orphan / churned) |
| "Pricing in the brain doesn't match the call transcript" | **C3** (cross-source conflict) |

   When in doubt, pick the closest match and add a one-line note in the Evidence body that says "Auto-triaged from manual report; recategorize if D2 isn't the right fit."

4. **Find an existing parent** that matches `(category_code, broad scope)`:
   - Same category + same broad theme → file as subitem under it
   - No suitable parent → create a new parent using the standard naming convention from `monday-board-schema.md` (e.g., `Uneven Category Coverage — Services info missing in brain`)
5. **Set column values** on the new subitem (or the new parent if a parent didn't exist):
   - **Category** — taxonomy code
   - **Severity** — taxonomy default for that code
   - **Client** — affected client_id
   - **First Seen** / **Last Seen** — today
   - **Detection Source** — `Ad-hoc` (so the team knows this was manually reported, not auto-detected)
   - **Triage Status** — `New`
   - **Action Owner** — taxonomy default (E2M / CGN / Both)
   - **Dedup Key** — `{code}::{client_id}::{normalized-summary-hash}` per `dedup-logic.md`
6. **Delete** the original loose top-level item once the new subitem is filed (this avoids duplicates on the board).

## Don't be too aggressive

- Don't re-categorize items that already have Category + Severity + Owner filled, even if you'd categorize them differently. Those were intentionally classified.
- Don't merge items that look similar but aren't — when unsure whether two items are "the same issue," file as separate subitems and let the human merge.
- If you can't confidently identify a client, leave the original loose item in place and add an Update on it asking which client it's about. Don't guess and re-file.

## Reporting in the digest

Add a section to the Slack digest after "New issues this run":

```
*Auto-triaged manual reports:* {count}
• Original: "{loose title}" → reclassified as [{code} / {severity}] {client} → {parent_link}
```

This makes it visible to E2M and CGN that manual reports got caught and routed, and gives them a chance to push back if the categorization was wrong.

## Worked example — the Tribble case, 2026-04-28

A teammate added a top-level item with no metadata: "Tribbles brain did not have information about kitchen cabinet painting service, even though it is on their website"

Triage produced:
- Client: `tribble-painting` (fuzzy match "Tribbles" → Tribble Painting in the Active SEO board)
- Category: `D2` (uneven category coverage — Services & Locations missing for this client)
- Severity: `Medium`
- Owner: `Both` (CGN sources the missing service material; E2M re-ingests)
- Parent: created `Uneven Category Coverage — Services info missing in brain` (no existing D2 parent yet)
- Subitem: `tribble-painting (36 docs) — kitchen cabinet painting service missing in brain, present on website`
- Detection Source: `Ad-hoc`
- Original loose item: deleted