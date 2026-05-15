# Slack passive-scan patterns

## Channel scope

Scan **exactly these 10 internal CGN channels** (Logan's directive 2026-04-28). Do NOT scan DMs and do NOT scan client-specific channels. Search over the last **7 days** by default (30 days on the first-ever run for a new installer).

| Channel | ID | Why it's in scope |
|---|---|---|
| `#general` | `C01RPGHG11T` | All-hands — top-level brain mentions |
| `#internal-marketing` | `C02KE8DEYCS` | Internal marketing coordination |
| `#ai-tools` | `C08UWE92WTG` | Where AI/brain tooling questions go |
| `#sales` | `C02KBH01669` | Sales team — surfaces accuracy concerns |
| `#client-success` | `C02KGNL897E` | Client Success — surfaces client pushback |
| `#seo` | `C04S2LU3GHY` | SEO team — primary brain users for content |
| `#webdev` | `C03S3CDS0MT` | Web dev — site copy + brain integration |
| `#tide-pod` | `C0AH204EL13` | Pod channel — content/SEO workflows |
| `#naskr-pod` | `C0AHWM2G9S9` | Pod channel — Patrick's testing happens here |
| `#e2m-and-cgn` | `C0ADRFD1S59` | E2M/CGN coordination — high brain-conversation volume; also the digest destination |

**Out of scope:**
- DMs (any direction)
- Client-specific channels (e.g., `#frei-remodeling`, `#the-remodel-group`)
- Notification/log channels (e.g., `#portal-notifications`, `#n8n-notifications`)

**Note on `#e2m-and-cgn`:** the sweep both scans it AND posts the digest there. The digest is a top-level message; scanning is keyword-based across recent threads. They don't conflict.

If new pod or team-functional channels are created, add them here. The rule is: cross-functional CGN internal channels where the team discusses using the brain or producing client content.

## Keyword patterns

Run each pattern as a Slack search scoped to the channel set above (`in:#channel-name` per channel, OR a multi-channel query). For each hit, capture the permalink as evidence.

### Access & reliability signals → A-codes

- "not authenticated"
- "can't log in" OR "cant login" OR "can't connect"
- "stale" (near "brain" or "connector")
- "reconnect"
- "brain is down" OR "brain not working"
- "remove and re-add" OR "remove and readd"

### Data integrity signals → B-codes

- "duplicate" (near "client" or brain-related terms)
- "0 documents" OR "zero docs"
- "empty" (near client name or "brain")
- "missing client" OR "not in the brain"

### Accuracy signals → C-codes

- "inaccurate" OR "inaccuracy"
- "not right" OR "wrong"
- "incorrect"
- "conflict" OR "conflicting"
- "contradicts" OR "contradict"
- "hallucinat"
- "made up" OR "made-up"
- "fact check" OR "factcheck"
- "client pushed back" OR "client feedback"
- "embarrassing" (past experience shows this is Logan's word for accuracy failures)

### Retrieval / completeness signals → D and E codes

- "can't find" OR "cant find"
- "no results"
- "didn't find"
- "empty response"
- "didn't have information" OR "doesn't have info" (often → D2 service-info gaps, like the 2026-04-28 Tribble case)
- "missing from the brain" OR "not in the brain"

### UX signals → F-codes

- "timeout" OR "timing out"
- "slow" (near "brain" or client query context)
- "confused" OR "confusing"
- "not sure if" (near CGN/E2M distinction)

## Triage rules

- A single hit alone rarely files an issue. Look for the **hit + context**: if the surrounding messages describe a concrete issue, file it. If it's speculative or general venting, skip it.
- For ambiguous hits, the skill should ask the teammate in its next interactive session rather than guessing.
- Client-name extraction: when filing, scan the message and nearby context for any client ID from `list_clients` (apply override mappings in `client-id-mappings.md` for renames). Attach it to the monday item's Client field.

## Context harvesting

When you find a hit, also pull `include_context=true` and the 2–3 surrounding messages so the monday item captures enough detail for someone else to understand the issue without opening Slack.