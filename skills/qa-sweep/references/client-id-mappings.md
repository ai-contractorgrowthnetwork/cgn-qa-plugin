# Client ID mappings

Brain client IDs don't always match the client's current name in the Active SEO Clients monday board. Renames, POD transfers, and abbreviation conventions cause drift. This file is the authoritative override list — probe **P5** (active SEO roster diff) consults it before falling back to fuzzy matching.

## How the diff uses this file

For each Active SEO client pulled from monday, the sweep checks:
1. **Explicit override** in this file (this is authoritative — skip fuzzy match)
2. **Normalized fuzzy match** (lowercase, hyphen/underscore equiv, drop suffixes like LLC/Inc, drop POD TRANSFER notes, Levenshtein ≤ 3)
3. **No match** → treat per the B3/B4 rules in `active-probes.md`

When a B7 (stale client ID) is confirmed by the team, add a row here so future sweeps treat the rename as resolved.

## Confirmed mappings (overrides)

| Brain ID | Active SEO board name | Active SEO board item ID | Notes |
|---|---|---|---|
| `apex-home-innovations` | Lifestyle Design + Remodel (POD TRANSFER) | 10745358360 | Confirmed by Logan 2026-04-27. Brain ID is a stale name from before the company became Lifestyle Design + Remodel. 40 docs are silent to queries by the current name until E2M renames the brain ID. |
| `wisa-solutions` | WISA Design Build Remodel | 18246439601 | Confirmed by Logan 2026-04-27. Same company, renamed. 173 docs silent under the old ID. |
| `lifestyle-cc` | Lifestyle Custom Construction | 11593612184 | Confirmed by Logan 2026-04-27. The `cc` suffix = "Custom Construction". This is a different company from Lifestyle Design + Remodel — keep them separate. |

## Normalization rules (for fuzzy match fallback)

When no explicit override applies, normalize both sides before comparing:

1. Lowercase
2. Replace `_` with `-`
3. Drop everything in parentheses (e.g., `(POD TRANSFER)`, `(Williamson Building & Remodeling)`)
4. Strip common suffixes: ` llc`, ` inc`, ` co`, ` company`, ` & co`
5. Strip common articles/separators: leading `the `, `&`, `+`, `°`, `'`
6. Collapse multiple hyphens to one
7. Strip leading/trailing hyphens

After normalization, compare with Levenshtein distance ≤ 3 as a positive match.

### Examples

| Active SEO board name | Normalized | Brain ID | Normalized | Match? |
|---|---|---|---|---|
| 360° Construction | `360-construction` | `360-construction` | `360-construction` | ✅ exact |
| T.H.E. Remodel Group | `the-remodel-group` (drop "the") → `remodel-group` | `the-remodel-group` | `remodel-group` | ✅ within Lev 3 |
| WBR (Williamson Building & Remodeling) | `wbr` (parens dropped) | `wbr-williamson-building-remodeling` | `wbr-williamson-building-remodeling` | ❌ would fail naive — keep parens content as fallback |
| Buckingham Bath & Home Remodeling (POD TRANSFER) | `buckingham-bath-home-remodeling` | `buckingham-bath-home-remodeling` | `buckingham-bath-home-remodeling` | ✅ exact after dropping parens |

The WBR case shows fuzzy matching is fragile — explicit overrides are the safer path for any client with a complex name. When in doubt, file a B7 and let the human confirm.

## Adding new mappings

When a new B7 is confirmed by Logan/E2M:

1. Add a row to the **Confirmed mappings** table above with brain ID, monday name, monday item ID, and confirmation note + date
2. Mark the original B7 monday item as Resolved with a reference to this file
3. Future sweeps will treat the rename as authoritative and won't re-flag

When a brain ID is RENAMED in the brain itself (E2M completes a rename action):

1. Update the row in this file to use the new brain ID
2. Move the previous brain ID to a "Historical (now resolved)" section at the bottom of this file for traceability

## Historical (resolved renames)

(empty — populate as renames complete)