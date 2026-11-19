# agent-skills

[English](README.md) | 中文

个人维护的 Agent Skills，遵循 [Agent Skills 规范](https://agentskills.io)，可用于 Claude Code、Codex 等支持该规范的 agent。

## 安装

```sh
npx skills add WangHL0927/agent-skills --skill <名字>
```

加 `-g` 安装到用户级（所有项目可用）。也可以直接把 `skills/<名字>/` 目录复制或链接到 agent 的 skills 目录。

## 列表

| 名字 | 用途 |
|---|---|
| [stakeholder-review](skills/stakeholder-review/SKILL.md) | 多方评审：按决策的真实利益相关方各派一个独立 agent 调研出方案或评审已有方案，由独立的仲裁 agent 汇总、按需再派一轮并拍板，主会话只做协调。 |

skill 正文为英文，执行时会用用户的语言沟通和出报告。

## 许可

MIT
