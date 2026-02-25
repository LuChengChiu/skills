# Skills

A personal collection of Claude Code skills developed by [@luchengchiu](https://github.com/luchengchiu).

## What Are Skills?

Skills are reusable prompt modules that extend Claude Code's capabilities for specific tasks. Each skill lives in its own directory under `skills/` and contains a `SKILL.md` that defines when and how Claude should activate it.

## Skills Index

| Skill | Description |
|-------|-------------|
| [fetch-job-description](skills/fetch-job-description/SKILL.md) | Extract structured job data from 104.com.tw or Cake.me job posting URLs |

## Structure

```
skills/
└── <skill-name>/
    ├── SKILL.md      # Skill definition and instructions
    └── schema.md     # (optional) Data schemas or supporting reference
```

## Usage

Skills are picked up automatically by Claude Code when installed. Claude detects the appropriate skill based on the trigger phrases and context described in each `SKILL.md`.

To invoke a skill manually, use the slash command syntax:

```
/<skill-name> [arguments]
```

## Adding a New Skill

1. Create a new directory under `skills/`
2. Add a `SKILL.md` with a YAML frontmatter block (`name`, `description`, `metadata`) and instructions
3. Optionally add supporting files (e.g. `schema.md`, `examples/`)
