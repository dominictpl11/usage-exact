---
name: usage-exact
description: "Report current Codex quota usage with exact local-time reset timestamps to the second. Use when the user asks for exact Codex quota reset times or invokes /usage-exact or $usage-exact."
license: MIT
compatibility: "Requires Node.js 22.19+ and network access for the quota-axi CLI. Designed for Codex and Claude Code."
metadata:
  short-description: Exact Codex quota reset report
---

# usage-exact

When invoked as `$usage-exact` in Codex or `/usage-exact` in Claude Code, run this read-only command:

```text
npx -y quota-axi --provider codex --json --no-credential-refresh
```

Do not reset limits, consume reset credits, refresh credentials, or replace live quota data with local session-log estimates.

Read the `providers[]` entry whose `provider` is `codex`. Use its `generatedAt` value as the snapshot clock. Detect the host's configured local timezone at runtime; use `Intl.DateTimeFormat().resolvedOptions().timeZone` when Node.js is available, and do not hard-code `Asia/Shanghai`. Convert the snapshot and every `resetsAt` from UTC to that local timezone, calculate `usedPercent = 100 - percentRemaining`, and calculate remaining time as `max(0, floor(resetsAt - generatedAt))` seconds.

Map windows as follows:

- `five_hour` → `5-hour`
- `weekly` → `7-day`
- `kind: model` or an id beginning with `model:` → `Model window` (include the window label in parentheses when there is more than one)

If `resetsAt` is missing, use `no reset time (n/a)`. If a percentage is missing, use `usage unavailable`; never estimate it.

Return only this format, with no explanation:

```text
Query succeeded (local time snapshot: YYYY-MM-DD HH:mm:ss, Area/Location):

- 5-hour: X% used, resets at YYYY-MM-DD HH:mm:ss (in H hours M minutes SS seconds)
- 7-day: X% used, resets at YYYY-MM-DD HH:mm:ss (in H hours M minutes SS seconds)
- Model window: X% used, resets at YYYY-MM-DD HH:mm:ss (in H hours M minutes SS seconds)

Status: fresh.
```

Omit unavailable window lines. Use the provider's actual `state.status` in the final line. If the query fails, return only `Query failed: unable to read Codex quota (reason)` and do not expose tokens, account ids, or raw JSON.
