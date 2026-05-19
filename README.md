# Skill Matrix

Personal AI assistant skill library — SKILL.md files for Claude Code, Codex, Cursor, and other AI tools.

## Install as a Claude Code plugin (recommended)

```
# 1. Register this repo as a marketplace (once)
/plugin marketplace add zhangmin510/skill-matrix

# 2. Install plugins from the marketplace
/plugin install cubox-classify
```

Claude Code handles cloning, caching, and activation automatically. All plugins are registered in `.claude-plugin/marketplace.json`.

## Install via symlink (development / other tools)

For development (changes reflect immediately without reinstall):

```bash
ln -sf "$(pwd)/skills/cubox-classify" ~/.claude/skills/
```

Restart your AI assistant after linking. Skills auto-trigger based on user input matching the `description` field in each `SKILL.md`.

## Skills

| Skill | What it does |
|-------|-------------|
| [cubox-classify](skills/cubox-classify/SKILL.md) | Auto-classify Cubox bookmarks into folders + tags |

## Adding skills

1. Create `skills/<name>/` directory
2. Add `SKILL.md` with YAML frontmatter (`name`, `version`, `description`)
3. Add `.claude-plugin/plugin.json` for plugin metadata
4. Register in `.claude-plugin/marketplace.json` under `plugins`
5. Symlink or install as plugin

See [AGENTS.md](AGENTS.md) for full structure details.
