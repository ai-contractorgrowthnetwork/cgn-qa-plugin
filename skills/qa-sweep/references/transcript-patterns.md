# Session transcript patterns

When scanning recent sessions (via `session_info.list_sessions` and transcript reads), look for the following. Map hits to taxonomy codes in `taxonomy.md`.

## Tool and integration errors

- CGN brain MCP tool failures, timeouts, or `auth_required` style messages → **A5** (and often **A1** if auth-related)

## User frustration near brain answers

Phrases or tone in the same turn or adjacent turns as a brain/tool response, for example:

- "that's not right", "that's wrong", "incorrect"
- "you're confusing clients", "wrong client", "mixed up"
- "hallucinating", "made that up", "nowhere in our docs"

→ **C1** (and **E1** if the complaint is specifically about slowness)

## Brain conflict signals

Assistant messages that explicitly state conflicting sources, contradictory facts, or "two different answers" about the same client fact → **C2** (harvest verbatim snippet for monday evidence)

## Knowledge-gap markers

- Literal token `[UNKNOWN - NEEDS CLIENT INPUT]` in generated or pasted content → **D1**

## Search tips

- Prefer threads where `cgn-brain`, `ask_qa`, `list_clients`, or Client Brain tools appear in the same session window.
- Capture session id and a short quoted excerpt for dedup keys and monday updates.
