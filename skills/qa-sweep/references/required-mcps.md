# Required MCPs

This plugin depends on each teammate having these MCPs connected in Cowork. On preflight, check each one. If any are missing, report which are missing and stop the sweep — don't try to run a partial sweep silently.

## Required (sweep fails without these)

| MCP | Purpose | How to detect |
|---|---|---|
| CGN brain (`cgn-brain` custom connector) | All active probes and canary queries | Tool `auth_status` or `list_clients` available |
| Slack | Passive Slack scan (Step 2) | Tool `slack_search_public_and_private` available |
| monday.com | (1) File and dedup issues on the CGN Brain Issues- testE2M board (board `18413317001`); (2) Active SEO clients roster diff against the Client Database board (board `18245097205`) for probe P5 | Tool `create_item` / `get_full_board_data` / `get_board_items_page` available |
| `session_info` | Transcript scan (Step 3) | Tool `list_sessions` available |
| `scheduled-tasks` | Self-scheduling on first run | Tool `create_scheduled_task` available |

## Optional (sweep degrades gracefully without these)

(none currently — HubSpot was previously listed as optional for the roster diff but is no longer used; see below.)

## Removed: HubSpot

HubSpot was previously the source of truth for "active client" in probe P5. It has been removed because:

- `lifecyclestage = customer` returns 293 historical records (incl. churned, completed, prospects), making the diff unreliable
- CGN's actual canonical list of active SEO clients lives in the monday "Client Database" board (`18245097205`), group **Active Clients**, Services Purchased contains "SEO"

The plugin no longer reads from HubSpot. If a HubSpot MCP is connected, it is ignored by this sweep.

## If required MCPs are missing

Report to the user in plain language, e.g.:

> I can't run the full CGN brain QA sweep — these MCPs aren't connected:
> - monday.com — needed to log findings AND to read the Active SEO clients roster
> - session_info — needed to scan my session history for brain issues
>
> Connect them and try again.

Do not proceed with a partial sweep silently. The team needs to trust that if the sweep posts clean, nothing is actually wrong.