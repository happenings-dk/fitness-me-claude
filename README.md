# Fitness Me for Claude

A Claude plugin that connects Claude to your private [Fitness Me](https://fitnessme.org) and teaches it how to read and log your training, food, weight, sleep and mood.

## Install

**Cowork / Claude:** Customize → Plugins → **Add marketplace** → `happenings-dk/fitness-me-claude` → install **Fitness Me** → sign in with your fitnessme.org account.

**Claude Code:**

```sh
claude plugin marketplace add happenings-dk/fitness-me-claude
claude plugin install fitness-me@fitness-me
```

**Claude chat (web/mobile), without plugins:** Customize → Connectors → **+** → Add custom connector → `https://fitnessme.org/api/mcp`. Optionally upload the skill zip from fitnessme.org → Settings → Connected apps.

## What's inside

- **Connector:** `https://fitnessme.org/api/mcp` (OAuth; you choose read-only or "log and change things" when you approve).
- **Skill `fitness-me`:** how to use the `fm_command` tool well.
- **Commands:** `/fitness-me:today`, `/fitness-me:weigh-in`, `/fitness-me:log-meal`, `/fitness-me:weekly-review`.

Your data stays in your Fitness Me account; this repository contains no data or credentials. Disconnect anytime in fitnessme.org → Settings → Connected apps.

> Generated from the private `fitness-me` repo (`claude-plugin/`). Don't edit here.
