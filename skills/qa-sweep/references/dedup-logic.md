# Dedup logic

Multiple teammates run this sweep on their own machines. Without dedup, the same issue gets filed N times. This file defines how to collapse duplicates.

## Dedup key construction

For each detected issue, build a key string:

```
dedup_key = f"{category_code}::{client_id_or_na}::{normalized_summary_hash}"
```

Where:
- `category_code` is from `taxonomy.md` (e.g., `C2`)
- `client_id_or_na` is the affected client ID, lowercased, or `na` if not client-specific
- `normalized_summary_hash` is a short SHA-ish of the issue's key content:
  - For Slack-scan hits: the lowercased first 80 chars of the matched message stripped of punctuation
  - For transcript hits: the lowercased first 80 chars of the matched assistant text
  - For active-probe hits: a fixed string per probe outcome (e.g., `b1-duplicate-charleene`)
  - For canary hits: `{canary_id}-{pass_or_fail_flavor}`

## Matching against existing items

Before creating a new monday item:

1. Query the "CGN Brain Issues- testE2M" board for items where:
   - Dedup key field matches exactly, AND
   - Status is not "Resolved" (resolved items can come back as new occurrences)
2. If a match is found:
   - Append an update to the existing item: "Re-affirmed by {reporter} on {date}. Evidence: {new evidence link}."
   - Increment the Occurrence Count column
   - Update Last Seen to today
   - Do NOT create a new item
3. If no match:
   - Create a new item with all the standard fields
   - Set Occurrence Count to 1
   - Set First Seen and Last Seen to today
   - Set Status to "New"

## Edge cases

- **Resolved items reappearing:** if dedup_key matches a RESOLVED item, create a new item instead of reopening. This way we can track regressions separately from the original issue. Add a reference to the prior item's URL in the Evidence field: "Previously resolved: {url}".
- **Cross-teammate same-day collisions:** two teammates run the sweep simultaneously and both file the same new issue. Acceptable race — monday will have two items briefly. Next sweep will dedup the second one into the first. Don't over-engineer locking.
- **Summary drift:** the same underlying issue might get normalized to slightly different summary hashes across runs (e.g., message text mutates over time). That's fine — the result is at most one extra item, and a human can merge on triage. Don't try to be clever with fuzzy hash matching.