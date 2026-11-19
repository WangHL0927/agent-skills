# agent-skills

English | [中文](README.zh-CN.md)

Personal agent skills following the [Agent Skills specification](https://agentskills.io). They work with Claude Code, Codex, and other agents that support it.

## Install

```sh
npx skills add WangHL0927/agent-skills --skill <name>
```

Add `-g` to install at user level (available in every project). You can also copy or symlink `skills/<name>/` into your agent's skills directory.

## Skills

| Name | What it does |
|---|---|
| [stakeholder-review](skills/stakeholder-review/SKILL.md) | Multi-stakeholder review: one independent agent per real stakeholder investigates and proposes (or reviews an existing proposal); an independent arbiter consolidates, runs further rounds if needed, and decides; the main session only coordinates. |

Skills are written in English; when running, they communicate and report in the user's language.

## License

MIT
