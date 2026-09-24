---
name: usage-exact
description: "查询当前 Codex 额度，并用中文显示 5 小时、7 天和模型窗口的已用百分比、北京时间秒级重置时间、剩余时间及返回状态。Use when the user asks for exact Codex quota reset times or invokes usage-exact."
user-invocable: true
metadata:
  short-description: Exact Codex quota reset report
---

# usage-exact

When invoked, run this read-only command:

```text
npx -y quota-axi --provider codex --json --no-credential-refresh
```

Do not reset limits, consume reset credits, refresh credentials, or replace live quota data with local session-log estimates.

Read the `providers[]` entry whose `provider` is `codex`. Use its `generatedAt` value as the snapshot clock. Convert every `resetsAt` from UTC to `Asia/Shanghai`, calculate `usedPercent = 100 - percentRemaining`, and calculate remaining time as `max(0, floor(resetsAt - generatedAt))` seconds.

Map windows as follows:

- `five_hour` → `5 小时`
- `weekly` → `7 天`
- `kind: model` or an id beginning with `model:` → `模型窗口` (include the window label in parentheses when there is more than one)

If `resetsAt` is missing, use `没有重置时间（n/a）`. If a percentage is missing, use `已用不可用`; never estimate it.

Return only this format, with no explanation:

```text
查询成功（北京时间快照：YYYY-MM-DD HH:mm:ss）：

- 5 小时：已用 X%，YYYY-MM-DD HH:mm:ss 重置（还有 H 小时 M 分 SS 秒）
- 7 天：已用 X%，YYYY-MM-DD HH:mm:ss 重置（还有 H 小时 M 分 SS 秒）
- 模型窗口：已用 X%，YYYY-MM-DD HH:mm:ss 重置（还有 H 小时 M 分 SS 秒）

返回状态：fresh。
```

Omit unavailable window lines. Use the provider's actual `state.status` in the final line. If the query fails, return only `查询失败：无法读取 Codex 额度（原因）` and do not expose tokens, account ids, or raw JSON.
