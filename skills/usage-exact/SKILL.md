---
name: usage-exact
description: "Report exact local-time quota reset windows in the language of the user's request. Use when the user asks about quota usage or reset times, or invokes /usage-exact or $usage-exact."
license: MIT
compatibility: "Requires Node.js 22.19+ and network access for quota-axi. Portable across Codex, Claude Code, and compatible Agent Skills hosts."
metadata:
  short-description: Exact local-time quota reset report
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

Before formatting, detect the predominant language of the invoking user message. Respond in that language, including the success line, window labels, missing-data text, failure text, and status label. Do not switch languages because this repository is documented in English, because the host locale differs, or because the timezone identifier is English. For mixed or ambiguous input, use the language of the user's latest direct request; use English only when no language can be determined. Preserve the field order, numeric precision, timezone identifier, percentages, and provider status in every language.

If `resetsAt` is missing, localize `no reset time (n/a)`. If a percentage is missing, localize `usage unavailable`; never estimate it.

Return only this format, with no explanation. The following is the English reference; translate its prose and labels into the selected language when needed:
Use Markdown bold emphasis for the key fields exactly as shown. Keep the bold spans around the localized equivalents of the success state, snapshot time, timezone, window name, usage, reset time, countdown, and final status.

```text
**Query succeeded** (local time snapshot: **YYYY-MM-DD HH:mm:ss**, **Area/Location**):

- **5-hour:** **X% used**, resets at **YYYY-MM-DD HH:mm:ss** (**in H hours M minutes SS seconds**)
- **7-day:** **X% used**, resets at **YYYY-MM-DD HH:mm:ss** (**in H hours M minutes SS seconds**)
- **Model window:** **X% used**, resets at **YYYY-MM-DD HH:mm:ss** (**in H hours M minutes SS seconds**)

**Status:** **fresh**.
```

Omit unavailable window lines. Use the provider's actual `state.status` in the final line. If the query fails, localize `Query failed: unable to read quota data (reason)` and do not expose tokens, account ids, or raw JSON.
