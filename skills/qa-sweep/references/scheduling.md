# Self-scheduling (first-run setup)

On the very first invocation, the skill should schedule itself to run every 2 days via the `scheduled-tasks` MCP so the sweep becomes passive.

## How to detect first run

Check for an existing scheduled task named `cgn-brain-qa-sweep`. If none exists, this is the first run.

## Create the scheduled task

Call `scheduled-tasks.create_scheduled_task` with:

- **name:** `cgn-brain-qa-sweep`
- **description:** "Automated CGN brain QA sweep. Runs every 2 days."
- **cadence:** every 2 days (use whatever interval-spec the MCP requires)
- **trigger prompt:** "Run the qa-sweep skill from the cgn-brain-qa plugin. This is an automated scheduled run — post the digest to Slack but skip the in-session user summary."

## After scheduling

Confirm to the user (in plain language):
- The sweep is now scheduled to run every 2 days
- They can run it manually anytime with "run cgn brain qa"
- They can stop the schedule anytime with "stop the cgn brain qa schedule"

## Timezone and timing

Schedule at a time when the user's machine is most likely on and Cowork is running. Default to weekday mornings local time. If the task fires and Cowork isn't running, the `scheduled-tasks` MCP will queue it for the next startup — that's acceptable.

## Stopping the schedule

If the user says "stop the cgn brain qa schedule" or similar, call `scheduled-tasks.update_scheduled_task` to disable it, or `delete` it entirely per the MCP's API. Confirm to the user.