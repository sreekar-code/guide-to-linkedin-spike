# Spike.sh LinkedIn Post Generator

Reads published guides from Notion and generates LinkedIn posts using Claude Code + Notion MCP. No scripts, no API keys, no setup.

## How it works

Open Claude Code in this project and say:

> "Generate posts for unprocessed guides"

Claude will:
1. Find all guides where **Status = Published** and **Posts Generated = unchecked**
2. Pull the last 10 **Finalized** posts as style reference (skipped if none exist yet)
3. Pull the last 10 **Published** posts with impressions data as analytics context (skipped if none exist yet)
4. Generate posts — one per sharp idea extracted from the guide
5. Show you the posts for review
6. Write them to the LinkedIn Posts DB (Status = Generated) and mark the guide's Posts Generated = ✓

## Files

| File | Purpose |
|---|---|
| `instructions.txt` | Tone guidelines, post structure rules, and reference post. Edit to update the writing brief. |
| `.env` | Notion database IDs (used by Claude to know where to read/write) |

## Notion databases

| Database | Fields used |
|---|---|
| **Guides** | Title, Status, Posts Generated |
| **LinkedIn Posts** | Title, Post Content, Status, Linked Guide, Impressions, Engagements, Engagement Rate, Clicks, Click-through Rate, Reactions, Comments, Reposts, Notes |

## Post lifecycle

**Generated** → review and edit in Notion → **Finalized** → publish on LinkedIn → **Published** → fill in analytics fields

As you accumulate Finalized and Published posts with analytics, those automatically feed into context on future generation runs.
