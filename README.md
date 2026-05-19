# Skill Matrix

Personal AI assistant skill library — SKILL.md files for Claude Code, Codex, Cursor, and other AI tools.

## Quick install

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

1. Create `skills/<name>/SKILL.md`
2. Add YAML frontmatter (`name`, `version`, `description`)
3. Write the skill body — steps, commands, edge cases, examples
4. Symlink and restart

See [AGENTS.md](AGENTS.md) for full structure details.
