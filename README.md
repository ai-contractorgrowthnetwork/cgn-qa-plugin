# CGN Brain QA Plugin (`cgn-brain-qa`)

Claude / Cursor plugin for **CGN Brain health QA**: scheduled sweeps and on-demand issue reporting to **Slack** and **monday.com**. It compares brain inventory to business records, scans transcripts and Slack for quality signals, runs canary checks, and files deduplicated issues on a central Monday board.

See **`SYSTEM_DESIGN.md`** for the full architecture and workflow.

---

## Skills

| Skill | When it runs | Purpose |
|--------|----------------|--------|
| **`qa-sweep`** | On demand (“run cgn brain qa”, “cgn brain audit”, …) or every 2 days if `scheduled-tasks` is set up (`references/scheduling.md`) | Full health sweep: probes, Slack/session scans, roster diff, canary queries, Monday filing, digest |
| **`report-issue`** | On demand (“log a brain issue”, “file a cgn brain bug”, …) | File a single issue with taxonomy + dedup, without a full sweep |

Skill entry points:

- `skills/qa-sweep/SKILL.md`
- `skills/report-issue/SKILL.md`

---

## MCP prerequisites

The sweep expects several MCP servers (session transcripts, monday.com, CGN brain, Slack, etc.). The authoritative checklist is:

**`skills/qa-sweep/references/required-mcps.md`**

If a required MCP is missing, the corresponding steps are skipped or the audit degrades as documented there.

---

## monday.com boards (two roles)

| Role | Purpose | Board ID in skills (current) |
|------|---------|------------------------------|
| **Issues** | File, dedup, manual triage, digest links | **`18413317001`** — *CGN Brain Issues- testE2M* (see `skills/qa-sweep/SKILL.md`) |
| **Client Database** | Active SEO roster for Monday vs Brain diff (probe P5) | **`18245097205`** — unchanged when only the issues board is sandboxed |

Column layout for the issues board is defined in **`skills/qa-sweep/references/monday-board-schema.md`**.

### Pointing issues at a different Monday board

There is no env file: the issues **board ID** is repeated in the markdown under `skills/`. To use another board (e.g. production **CGN Brain Issues**), duplicate its column structure in Monday, then replace the issues board ID (and display name where it helps) in:

- `skills/qa-sweep/SKILL.md`
- `skills/qa-sweep/references/required-mcps.md`
- `skills/qa-sweep/references/manual-item-triage.md`
- `skills/qa-sweep/references/dedup-logic.md` (wording only, if it names the board)
- `skills/report-issue/SKILL.md` (wording for which board to query)
- `skills/qa-sweep/references/slack-digest-format.md` (board URL in the digest template)

Leave **`18245097205`** as-is unless you intentionally move the roster source for P5.

---

## Key reference docs

| Path | Contents |
|------|-----------|
| `SYSTEM_DESIGN.md` | End-to-end blueprint, scans, taxonomy overview |
| `skills/qa-sweep/references/taxonomy.md` | Issue codes (A1–F3, …) |
| `skills/qa-sweep/references/dedup-logic.md` | Fingerprinting and re-affirmation |
| `skills/qa-sweep/references/active-probes.md` | Probes including P5 roster diff |
| `skills/qa-sweep/references/client-id-mappings.md` | Brain ID ↔ Monday name overrides |
| `skills/qa-sweep/references/slack-digest-format.md` | `#e2m-and-cgn` digest shape |

---

## Plugin manifest

Defined in **`.claude-plugin/plugin.json`** (name, version, description for the marketplace / installer).

---

## Contributing / fork notes

Keep **SYSTEM_DESIGN.md** and the **skills** tree in sync when you change board IDs, Slack channels, or probe behavior so automated and manual runs stay consistent.
