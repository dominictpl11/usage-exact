# usage-exact

A portable Agent Skill for compact, exact quota reset reports.

## What it does

`usage-exact` delegates a live quota query to [`quota-axi`](https://github.com/kunchenguid/quota-axi), then formats the result using the invoking request's language:

- 5-hour window
- 7-day window
- model-specific windows when available
- used percentage
- reset time in the host's local timezone, precise to seconds
- remaining time and provider status
- stable field order and numeric precision across supported languages

## Scope and data source

The skill is host-agnostic: install it in Codex, Claude Code, or another compatible Agent Skills host. The included read-only preset currently reads quota-axi's `codex` provider; the host used to invoke the skill and the provider being reported are separate concerns.

The skill uses:

```text
npx -y quota-axi --provider codex --json --no-credential-refresh
```

It is read-only: it does not reset limits, consume reset credits, or refresh credentials.

## Example output

```text
Query succeeded (local time snapshot: 2026-09-24 11:21:33, Asia/Shanghai):

- 5-hour: 14% used, resets at 2026-09-24 14:07:40 (in 2 hours 46 minutes 06 seconds)
- 7-day: 95% used, resets at 2026-09-26 19:35:06 (in 56 hours 13 minutes 32 seconds)
- Model window: 37% used, resets at 2026-09-24 21:48:44 (in 10 hours 27 minutes 10 seconds)

Status: fresh.
```

The example uses `Asia/Shanghai` only as a sample local timezone. Runtime output follows the host's configured timezone.

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

- Codex, Claude Code, or another compatible host running where `quota-axi` can access the configured provider credentials
- Node.js 22.19 or newer
- Network access for the upstream `quota-axi` CLI to query the first-party quota endpoint

## Privacy and safety

The skill does not print tokens, account IDs, or raw JSON. It asks `quota-axi` for a read-only quota report and only formats the returned window data. Review the upstream implementation and its privacy notes before installing updates.

## Attribution

This skill is a focused formatting fork built on the live quota-query workflow from [`kunchenguid/quota-axi`](https://github.com/kunchenguid/quota-axi). The upstream project is licensed under MIT.

## License

MIT. See [LICENSE](LICENSE).
