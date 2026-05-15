# Slack digest format

After each sweep, post a single threaded digest to `#e2m-and-cgn` (channel ID `C0ADRFD1S59`).

## When to post

- **Post** if the sweep filed any new issues, re-affirmed any existing issues, or resolved any issues.
- **Post** if the sweep itself failed (so the team knows a run was missed).
- **Don't post** if nothing changed and nothing is new. Silence is fine — the monday board is the source of truth.

## Template

```
:brain: CGN Brain QA Sweep — {date} — by {reporter_name}

*System status:* {:white_check_mark: / :warning: / :x:} {one_line_summary}

*New issues this run:* {count}
• [{category_code}] {short_summary} — {client_id}  → {monday_item_url}
• ...

*Re-affirmed open issues:* {count} (see board for details)

*Resolved since last run:* {count}
• {short_summary} — {monday_item_url}

Board: https://contractor-growth-network.monday.com/boards/{BOARD_ID}
```

## System status rules

- :white_check_mark: — all probes passed, Slack/transcript scans found nothing new or only Low-severity items
- :warning: — at least one Medium-severity issue filed, or >3 re-affirmed issues
- :x: — at least one High-severity issue filed (auth failure, data integrity, accuracy issue)

## Tone

Flat and factual. Not a marketing post. The audience is E2M + CGN leadership — they're triaging, not reading a narrative.

## Threading

Post all digests as top-level messages. Do NOT thread digests onto a previous digest — they should be scannable independently in the channel.

## Mentions

Do not @-mention anyone by default. If a High-severity issue files, the monday item assignment handles notification separately. Over-mentioning in Slack trains people to ignore these digests.