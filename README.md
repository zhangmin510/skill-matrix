# Skill Matrix

Personal AI assistant skill library — SKILL.md files for Claude Code, Codex, Cursor, and other AI tools.

## Install as a Claude Code plugin (recommended)

```bash
# From Claude Code, install a plugin from this repo:
/install-plugin cubox-classify --from git@github.com:zhangmin510/skill-matrix.git
```

Claude Code handles cloning, caching, and activation automatically. The plugin is registered in `.claude-plugin/marketplace.json`.

## Install via symlink (development / other tools)

```bash
# All skills → your AI assistant
ln -sf "$(pwd)/skills/"* ~/.claude/skills/

# Single skill
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
