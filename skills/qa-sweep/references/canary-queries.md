# Canary queries

Small set of known-answer questions run once per week to detect accuracy drift, cross-client bleed, and brand voice drift. Each canary is a triple: (client, question, expected-answer-snippet).

## Why canaries

Passive scanning only catches issues the team noticed. Canaries proactively check for issues the team didn't catch. They map to **C3, C6, C7, E3**.

## Baseline canary set

Start with this minimal set; expand over time as the team identifies high-signal queries.

| Client | Question | What a correct answer must contain |
|---|---|---|
| `7-day-kitchens` | What's the minimum project size for 7 Day Kitchens? | Reference to $60,000 minimum; should NOT anchor on $30,000. |
| `frei-remodeling` | What services does Frei Remodeling offer? | Should match the services listed in their most recent discovery form. |
| `df-carpentry` | What does Dusty charge for crown molding in a typical 2,000 sq ft home? | ~$5,500 reference (per the canonical Haiku enrichment example). |
| `bulletproof-pond-lake` | What geographic areas does Bulletproof Pond & Lake serve? | Their actual service area from their website/intake — NOT a generic answer. |
| `akn-interiors` | What's AKN's design aesthetic and brand voice? | Should reference their actual positioning docs, not generic interior-design language. |

## Running a canary

For each canary:

1. Ask the CGN brain the question, scoped to the specified client.
2. Compare the answer against the expected-content rule.
3. Additionally: run the same question against a *different* client (e.g., ask about DF Carpentry pricing while scoped to `frei-remodeling`). If the answer returns DF's pricing, that's **C6** (cross-client bleed).

## Filing results

- If the answer contains the expected content → no action.
- If the answer is missing the expected content but isn't outright wrong → **E2** (retrieval gap).
- If the answer is factually wrong → **C3** (cross-source conflict, because somewhere in the brain there's conflicting data).
- If the answer is in the wrong voice/industry → **C7**.
- If cross-client bleed → **C6**.

## Extending the canary set

When the brain catches a real **C1** or **C2** and the team fixes it, add a canary for that specific question so regressions get caught automatically. Over time this set becomes the team's regression test suite for the brain.