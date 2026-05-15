---
name: report-issue
description: Logs a single CGN brain issue on-demand without running a full sweep. Use when the user says "log a brain issue", "file a cgn brain bug", "report a brain problem", "flag this for the cgn brain", or describes a specific brain problem they just encountered and want captured.
---

# Report a CGN Brain Issue

One-shot logger for when a teammate hits something and wants to file it immediately instead of waiting for the next scheduled sweep.

## Flow

1. **Gather the details.** Ask the user (via AskUserQuestion if needed):
   - Which client is affected? (or "not client-specific")
   - What happened? (one-sentence summary)
   - Any evidence link? (Slack permalink, session reference, screenshot)
2. **Classify.** Use the taxonomy in `../qa-sweep/references/taxonomy.md` to pick the right category code (A1–F3). If it's ambiguous, ask the user to pick from the two most likely options.
3. **Check for duplicates.** Query the "CGN Brain Issues- testE2M" monday board for open items matching `(category_code, client_id, normalized_summary)`. If a match exists, append an occurrence update instead of creating a new item.
4. **File it.** Create a monday item using the field schema in `../qa-sweep/references/monday-board-schema.md`. Set Reporter to the current user, First Seen to today, Status to "New".
5. **Optional Slack ping.** Ask if they want a one-line notice posted to `#e2m-and-cgn` linking the new monday item. Default is no — the scheduled sweep's digest will surface it.

## When to push back

- If the issue sounds like general AI / LLM critique rather than a CGN-brain problem ("Claude made something up in an unrelated chat"), confirm with the user before filing. The board is scoped to CGN Brain Issues- testE2M specifically.
- If the user is actually describing a feature request (not a bug/gap), suggest logging it somewhere else and ask for confirmation before creating a monday item.