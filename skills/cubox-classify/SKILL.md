---
name: cubox-classify
version: 1.0.0
description: "Classify and tag uncategorized Cubox bookmarks by intelligently assigning folders and tags based on article content. Use this skill whenever the user wants to organize their Cubox inbox, clean up unclassified articles, bulk-assign folders or tags to saved bookmarks, or triage newly saved reading items. Trigger on: 'classify cubox', 'organize inbox', 'tag articles', 'sort uncategorized', 'clean up cubox', '整理未分类', '给文章分类', '分类整理', '打标签', '整理收藏', '清理cubox', '未分类文章'."
---

# Cubox Article Classifier

Intelligently assign folders and tags to uncategorized Cubox bookmarks by analyzing their titles, descriptions, and content.

## Workflow

### Step 1 — Fetch context (run in parallel)

```bash
cubox-cli folder list
cubox-cli tag list
cubox-cli card list --folder <uncategorized_id> --all
```

Find the uncategorized folder: look for the entry with `"uncategorized": true` in folder list. Its ID is the folder ID to use in `card list --folder`.

Tell the user how many uncategorized articles were found. If more than 30, ask whether to process in batches of 20 (default yes).

### Step 2 — Analyze and classify each card

For each card, read **title + description** and assign:

**Folder** (exactly one — pick the most specific match):

| Folder | When to use |
|---|---|
| Agent | AI agents, multi-agent systems, agentic workflows |
| AI编程 | AI-assisted coding, code generation, dev tools with AI (cursor, claude code, copilot) |
| AI变现 | AI monetization, side income, AI business models, solo entrepreneur AI products |
| AI行业 | AI industry news, company moves, funding, AI policy |
| AI模型 | LLM releases, model comparisons, training, architecture (GPT, Claude, Gemini, DeepSeek…) |
| AI评测 | Benchmarks, evals, model performance comparisons |
| AI产品 | AI product launches, app reviews, SaaS tools powered by AI |
| AI多模态 | Multimodal AI: vision, audio, video generation |
| 效率工具 | Productivity apps, workflow automation, note-taking tools (non-AI-specific) |
| Obsidian | Obsidian-specific plugins, workflows, knowledge management |
| Openclaw | Openclaw / OpenClaw project content |
| 技术 | Software engineering, system design, devops, programming (non-AI) |
| 面试算法 | Coding interview prep, algorithms, LeetCode |
| 金融投资 | Investing, stock market, crypto, personal finance |
| FIRE | Financial independence, early retirement, wealth building philosophy |
| 职场 | Career advice, workplace culture, management, job market |
| 人物观点 | Profiles of specific people, interviews, opinion pieces attributed to a person |
| 生活 | Lifestyle, health, relationships, personal development |

If no folder matches well, use `技术` for tech content or `生活` as a last resort. Avoid leaving anything in Uncategorized.

**Tags** (0–3 tags from the existing tag list, or propose a new lowercase tag if needed):

Known tags include: cubox, 第二曲线, 微信, claude-code, obsidian, anthropic, 教程, kimi, 案例, skills, 阿里, 百度, 林俊旸, 吴恩达, spec-kit, 知乎, copilot, fowler, antigravity, kiro, 杨振宁, 马云, sutton, aider, B站, 油管

Tag guidance:
- Use **source tags** (微信, 知乎, B站, 油管) when the domain makes the source obvious
- Use **person tags** (吴恩达, 马云, etc.) when the article is primarily about that person
- Use **tool/product tags** (claude-code, obsidian, kimi, copilot, aider, etc.) for product-specific articles
- Use `案例` for case studies or real-world examples
- Use `教程` for tutorials and how-to guides
- Suggest a **new tag** only if the topic is clearly distinct and likely to recur (e.g., a specific model, tool, or person not yet tagged)
- Do NOT force tags — 0 tags is better than irrelevant ones

### Step 3 — Present preview table

Show the full batch as a markdown table **before applying anything**:

```
Found 15 uncategorized articles. Here are my classification suggestions:

| # | Title | Folder | Tags |
|---|---|---|---|
| 1 | 2026年AI生意5条路… | AI变现 | 案例, 第二曲线 |
| 2 | html-anything开源！… | AI编程 | claude-code, 教程 |
| 3 | 年薪百万AI落地工程师… | AI行业 | — |
```

Then say: "Reply **apply** to execute all, or tell me any adjustments first (e.g., 'change #3 to 职场', 'remove tags from #5', 'skip #2')."

### Step 4 — Handle user adjustments

Accept natural language corrections:
- "Change #N to [folder]" → update suggestion for card N
- "Add tag [X] to #N" → add to suggested tags
- "Skip #N" → mark as skipped, won't be updated
- "Apply" / "好的" / "确认" / "全部执行" → proceed

### Step 5 — Apply changes

For each confirmed card (not skipped):

```bash
cubox-cli update --id CARD_ID --folder "FOLDER_NAME" --add-tag "TAG1,TAG2"
```

Rules:
- Use `--add-tag` (not `--tag`) to preserve any existing tags
- If a card has no tags to add, omit `--add-tag`
- Folder is by **name**, not ID
- Run updates sequentially if there are many (>10), reporting progress as you go

After all updates, print a compact summary:
```
✓ Updated 15 articles:
  AI行业 × 4, AI编程 × 3, AI变现 × 3, Agent × 2, 效率工具 × 2, 人物观点 × 1
  Skipped: 0
```

### Batch mode (20+ articles)

When processing in batches:
1. Label each preview: "Batch 1/3 (cards 1–20)"
2. After user confirms a batch, apply it and immediately show the next batch
3. Keep a running tally: "Applied 20/47, 27 remaining"

## Edge cases

- **Vague title, no description**: fetch card detail with `cubox-cli card detail --id CARD_ID -o text` and use the first 200 words to classify
- **WeChat articles** (mp.weixin.qq.com): classify by topic; add `微信` tag
- **YouTube / Bilibili**: add `油管` or `B站` tag respectively; classify by topic
- **Person-focused article**: use `人物观点` folder if the article is mainly about one person; add their name tag if it exists
- **Duplicate folder candidates**: prefer the more specific folder (e.g., `AI编程` over `技术`)
- **Already tagged but unfoldered**: the article may already have some tags — use `--add-tag` and just assign a folder
