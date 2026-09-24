# usage-exact

一个以英文 README 为主、面向 Codex 的中文额度查询 Skill，用固定格式显示当前 Codex 的重置时间。

[English README](README.md)

## 功能

`usage-exact` 调用 [`quota-axi`](https://github.com/kunchenguid/quota-axi) 查询实时额度，再按中文固定格式输出：

- 5 小时窗口
- 7 天窗口
- 可用时显示模型窗口
- 已用百分比
- 北京时间重置时间，精确到秒
- 距离重置的剩余时间和返回状态

使用的只读命令：

```text
npx -y quota-axi --provider codex --json --no-credential-refresh
```

不会重置额度、消耗 reset credit，也不会刷新凭据。

## 输出示例

```text
查询成功（北京时间快照：2026-09-24 11:21:33）：

- 5 小时：已用 14%，2026-09-24 14:07:40 重置（还有 2 小时 46 分 06 秒）
- 7 天：已用 95%，2026-09-26 19:35:06 重置（还有 56 小时 13 分 32 秒）
- 模型窗口：已用 37%，2026-09-24 21:48:44 重置（还有 10 小时 27 分 10 秒）

返回状态：fresh。
```

## 安装

全局安装到 Codex：

```bash
npx skills add dominictpl11/usage-exact --skill usage-exact -g -a codex -y
```

重启 Codex 或新建对话，然后输入：

```text
$usage-exact
```

Codex Skill 使用 `$` 调用。本仓库不会注册自定义的 `/usage-exact` 斜杠命令。

## 环境要求

- 已登录 ChatGPT 账号的 Codex
- Node.js 22.19 或更高版本
- 能让上游 `quota-axi` CLI 查询官方额度接口的网络连接

## 隐私与安全

Skill 不会输出 token、account ID 或原始 JSON，只读取并格式化 `quota-axi` 返回的额度窗口。更新上游依赖前，请先检查上游实现和隐私说明。

## 归属说明

本 Skill 是基于 [`kunchenguid/quota-axi`](https://github.com/kunchenguid/quota-axi) 实时额度查询流程制作的专用格式分支。上游项目使用 MIT 许可证。

## 许可证

MIT，详见 [LICENSE](LICENSE)。
