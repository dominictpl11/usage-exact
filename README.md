# usage-exact

An English-first, cross-agent Agent Skill that reports live Codex quota windows in a compact, predictable format.

[中文说明](README.zh-CN.md)

## What it does

`usage-exact` delegates the live quota query to [`quota-axi`](https://github.com/kunchenguid/quota-axi), then formats the result for Chinese-language users:

- 5-hour window
- 7-day window
- model-specific windows when available
- used percentage
- reset time in `Asia/Shanghai`, precise to seconds
- remaining time and provider status

The skill uses:

```text
npx -y quota-axi --provider codex --json --no-credential-refresh
```

It is read-only: it does not reset limits, consume reset credits, or refresh credentials.

## Example output

```text
查询成功（北京时间快照：2026-09-24 11:21:33）：

- 5 小时：已用 14%，2026-09-24 14:07:40 重置（还有 2 小时 46 分 06 秒）
- 7 天：已用 95%，2026-09-26 19:35:06 重置（还有 56 小时 13 分 32 秒）
- 模型窗口：已用 37%，2026-09-24 21:48:44 重置（还有 10 小时 27 分 10 秒）

返回状态：fresh。
```

## Install

### Codex

```bash
npx skills add dominictpl11/usage-exact --skill usage-exact -g -a codex -y
```

Restart Codex or start a new conversation, then invoke it with:

```text
$usage-exact
```

Codex uses `$` invocation.

### Claude Code

Install the same skill globally for Claude Code:

```bash
npx skills add dominictpl11/usage-exact --skill usage-exact -g -a claude-code -y
```

Restart Claude Code or start a new session, then invoke it with:

```text
/usage-exact
```

Claude Code derives the slash command from the `usage-exact` skill directory, so no separate `.claude/commands` wrapper is needed. You can also copy `skills/usage-exact/` to `~/.claude/skills/usage-exact/` for a personal install, or `.claude/skills/usage-exact/` for a project-only install.

The shared `SKILL.md` is the portable implementation. `skills/usage-exact/agents/openai.yaml` only supplies Codex-specific UI metadata.

## Requirements

- Codex or Claude Code running on the same machine where `quota-axi` can access the Codex quota credentials
- Node.js 22.19 or newer
- Network access for the upstream `quota-axi` CLI to query the first-party quota endpoint

## Privacy and safety

The skill does not print tokens, account IDs, or raw JSON. It asks `quota-axi` for a read-only Codex quota report and only formats the returned window data. Review the upstream implementation and its privacy notes before installing updates.

## Attribution

This skill is a focused formatting fork built on the live quota-query workflow from [`kunchenguid/quota-axi`](https://github.com/kunchenguid/quota-axi). The upstream project is licensed under MIT.

## License

MIT. See [LICENSE](LICENSE).
