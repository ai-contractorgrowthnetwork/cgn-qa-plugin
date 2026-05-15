# Issue taxonomy (category codes)

Use these exact codes when filing monday items or referencing findings. Pair each code with the severity heuristic below.

## Category codes

| Code | Area | Typical meaning |
| :--- | :--- | :--- |
| **A1** | System | CGN brain auth failure / unauthenticated |
| **A2** | System | Client list empty or clearly broken |
| **A3** | System | (Reserved / MCP connectivity degradation) |
| **A4** | System | (Reserved) |
| **A5** | System | Tool errors from CGN brain MCP in sessions |
| **A6** | System | (Reserved) |
| **B1** | Inventory | Near-duplicate client IDs with skewed doc counts |
| **B2** | Inventory | Zero-doc client not explained by active SEO roster |
| **B3** | Inventory | Brain client with docs, not on Active SEO list (orphan / churn) |
| **B4** | Inventory | Active SEO in monday, missing from brain |
| **B5** | Inventory | Stale ingestion / suspicious chunk counts |
| **B6** | Inventory | Active SEO client in brain with 0 docs |
| **B7** | Inventory | Stale client ID vs current monday name |
| **C1** | Quality | User frustration near brain answers |
| **C2** | Quality | Brain self-flagged conflicts in assistant output |
| **C3** | Quality | Canary / known-answer drift |
| **C4** | Quality | (Reserved) |
| **C5** | Quality | Hallucination or wrong factual claims |
| **C6** | Quality | (Reserved) |
| **C7** | Quality | (Reserved) |
| **D1** | Gaps | `[UNKNOWN - NEEDS CLIENT INPUT]` in generated docs |
| **D2** | Gaps | (Reserved) |
| **D3** | Gaps | (Reserved) |
| **E1** | Performance | Slow or timid responses (e.g. latency UX) |
| **E2** | Performance | (Reserved) |
| **E3** | Performance | (Reserved) |
| **F1** | Feedback | (Reserved — passive scan misc) |
| **F2** | Feedback | Slack keyword hit (operational) |
| **F3** | Feedback | Slack keyword hit (severity / follow-up) |

## Severity heuristic

- **High**: A1, A2, A5, B3, B4, B6, B7, C1, C2, C3, C5, C6
- **Medium**: A3, A4, A6, B1, B2, B5, C4, C7, D1, D2, D3, E1, E2, E3
- **Low**: F1, F2, F3

When in doubt, bias to the higher severity if user impact or data integrity is unclear.
