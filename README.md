# claude-skills

Reusable Claude skills for Claude Code, Cowork and chat. Each skill is a single folder containing a `SKILL.md` file: plain-English instructions that Claude loads when the task matches.

## Skills

| Skill | What it does |
|---|---|
| [brain-update](skills/brain-update/SKILL.md) | Summarises a chat, cowork or code session and updates the project's CLAUDE.md file(s): adds new knowledge, rewrites changed facts, removes superseded ones. Handles folders with multiple CLAUDE.md files. |

## Install

**Claude Code:** copy the skill's folder into `~/.claude/skills/` (all projects) or `<project>/.claude/skills/` (one project).

```
git clone https://github.com/dting007/claude-skills.git
cp -r claude-skills/skills/brain-update ~/.claude/skills/
```

**Claude (chat / Cowork):** zip the skill folder, rename the zip to `<skill-name>.skill`, and add it under Settings > Skills.

## Structure

```
claude-skills/
├── README.md
├── LICENSE
└── skills/
    ├── _template/SKILL.md     blank starter for a new skill
    └── brain-update/SKILL.md
```

One folder per skill, one `SKILL.md` per folder. Skills are independent of each other, so install only what you need.

## Adding a new skill

1. Copy `skills/_template` to `skills/<your-skill-name>`.
2. Edit the `name` and `description` in the frontmatter. The description decides when Claude uses the skill, so say when to use it.
3. Write the steps in plain English.
4. Add a row to the table above.

## Licence

MIT. Copyright (c) 2026 Ace Media Productions.
