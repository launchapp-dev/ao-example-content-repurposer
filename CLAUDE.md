# Content Repurposer — Agent Context

## What This Project Does

This is a content repurposing automation pipeline. It takes long-form content (blog posts, articles,
whitepapers) and transforms it into platform-native variants for Twitter/X, LinkedIn, Email,
Instagram, and YouTube.

## Directory Layout

```
content-repurposer/
├── .ao/workflows/          # AO workflow definitions
├── config/
│   └── sources.json        # INPUT: content sources + brand voice settings
├── data/                   # RUNTIME: intermediate files created during workflow
│   ├── source-content.json # Cleaned source text (written by ingest-content phase)
│   ├── content-analysis.json # Themes, quotes, hooks (written by analyze-content phase)
│   ├── review-feedback.json  # Per-variant feedback (written by review-quality on rework)
│   └── review-report.md    # Quality evaluation report (written by review-quality phase)
├── output/                 # FINAL: artifacts ready for publication
│   ├── summary.md          # Human-readable repurposing report
│   ├── calendar.json       # Publication schedule
│   └── variants/
│       ├── manifest.json   # Summary of all variants
│       ├── twitter-thread.md
│       ├── linkedin-post.md
│       ├── email-snippet.md
│       ├── instagram-caption.md
│       └── youtube-description.md
├── templates/              # Platform constraint guides for agents
├── sample-data/            # Demo content for testing
└── CLAUDE.md               # This file
```

## Workflow Phases

1. **ingest-content** — content-ingester (haiku) reads config/sources.json and fetches/reads the
   first source. Writes data/source-content.json. Decision: has-content | no-content.

2. **analyze-content** — content-analyzer (sonnet) reads source-content.json and uses
   sequential-thinking to extract themes, quotes, stats, and per-platform hooks.
   Writes data/content-analysis.json.

3. **generate-variants** — variant-writer (sonnet) reads content-analysis.json and generates all
   5 platform variants. Checks for data/review-feedback.json if in rework loop.
   Writes output/variants/*.md and output/variants/manifest.json.

4. **review-quality** — quality-reviewer (opus) reads all variants against source-content.json.
   Checks faithfulness, platform fit, consistency, engagement. Decision: publish | rework | reject.
   On rework: writes data/review-feedback.json with specific per-variant issues.
   Always writes data/review-report.md.

5. **finalize-output** — content-analyzer (sonnet) generates output/summary.md and
   output/calendar.json. Updates manifest.json status to "ready".

## Key Rules for Agents

- **Faithfulness is non-negotiable** — never invent facts, stats, or quotes not in the source
- **Platform constraints are hard limits** — Twitter 280 chars/tweet, LinkedIn 1300 chars, etc.
- **Brand voice from config/sources.json** — always check the brand_voice field before writing
- **Rework loop max 3 attempts** — the workflow enforces this; review-feedback.json tracks attempt count
- **Templates in templates/** — consult them for platform-specific formatting guidance

## Models Used

- **claude-haiku-4-5** — content-ingester (fast, cost-efficient for extraction)
- **claude-sonnet-4-6** — content-analyzer, variant-writer (creative writing and analysis)
- **claude-opus-4-6** — quality-reviewer (highest quality judgment, catches subtle errors)
