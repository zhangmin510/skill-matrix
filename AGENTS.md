# Skill Matrix

A personal skill library for AI coding assistants (Claude Code, Codex, Cursor, Aider, etc.).

## Structure

```
skill-matrix/
├── .claude-plugin/
│   └── marketplace.json      # Marketplace registration (Claude Code)
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md            # Skill definition (frontmatter + instructions)
│       └── .claude-plugin/
│           └── plugin.json     # Plugin metadata (Claude Code)
├── AGENTS.md                 # This file — project context for AI agents
├── CLAUDE.md                 # Symlink → AGENTS.md
└── README.md                 # Human-readable overview
```

## How to add a new skill

1. Create `skills/<skill-name>/SKILL.md`
2. Write YAML frontmatter:

```markdown
---
name: <skill-name>
version: 1.0.0
description: "When to trigger this skill and what it does."
---
```

3. Write the skill body — workflow steps, edge cases, commands, examples.

## Install as a plugin (Claude Code marketplace)

This repo is registered as a Claude Code plugin marketplace. Plugins can be
installed directly — Claude Code handles cloning, caching, and activation.

```bash
# From Claude Code, install a plugin from this repo:
/install-plugin cubox-classify --from git@github.com:zhangmin510/skill-matrix.git
```

Or symlink skills for development (changes reflect immediately):

```bash
# Install all skills
ln -sf "$(pwd)/skills/"* ~/.claude/skills/

# Install a single skill
ln -sf "$(pwd)/skills/cubox-classify" ~/.claude/skills/
```

After linking, restart Claude Code. Skills appear in `npx skills` and auto-trigger based on their `description` field.

## Auto-commit workflow

Whenever making changes to this repo, follow this workflow before asking for review:

### 1. Detect changes

```bash
git status --short
git diff --stat
```

### 2. Validate any SKILL.md changes

For each modified or new `skills/*/SKILL.md`:

```bash
# Verify frontmatter exists and has required fields
head -5 skills/<name>/SKILL.md | grep -q 'name:'
head -5 skills/<name>/SKILL.md | grep -q 'version:'
head -5 skills/<name>/SKILL.md | grep -q 'description:'
```

### 3. Update README.md (if adding/removing a skill)

If a new skill was added or removed, update the Skills table in `README.md` and `AGENTS.md` to match.

### 4. Commit

Stage only the files you changed — never `git add -A` (avoid accidentally committing secrets or temp files):

```bash
git add skills/<name>/SKILL.md  # changed skill
git add AGENTS.md README.md     # if tables were updated
```

Commit message format:

```
git commit -m "$(cat <<'EOF'
skill(<name>): <verb> <what>

<brief why this change was needed>
EOF
)"
```

Examples:
- `skill(cubox-classify): add batch mode for 30+ articles`
- `skill(cubox-classify): fix folder table markdown alignment`
- `skill(new-skill): add lark-calendar skill for meeting management`

### 5. Verify

```bash
git log --oneline -3
git status
```

Ensure the commit appears, no untracked files remain, and no merge conflicts exist.

## Installed skills

| Skill | Description |
|-------|-------------|
| [cubox-classify](skills/cubox-classify/SKILL.md) | Classify and tag uncategorized Cubox bookmarks by analyzing content |
