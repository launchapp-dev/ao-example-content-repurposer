# Content Repurposer — Workflow Plan

## Overview

Automated pipeline that ingests long-form content (blog posts, articles, whitepapers) from URLs or local files, extracts key themes, quotes, and structure, then generates platform-specific content variants — Twitter/X threads, LinkedIn posts, email newsletter snippets, Instagram captions, and YouTube descriptions. Each variant is adapted for platform tone, length constraints, and formatting conventions. Includes a quality gate to ensure derivatives are faithful to the source and meet platform standards.

Every content marketer manually does this today. This pipeline automates the entire flow using fetch MCP for URL ingestion, filesystem MCP for reading/writing, and sequential-thinking MCP for content strategy decisions.

## Agents

| Agent | Model | Role |
|---|---|---|
| **content-ingester** | claude-haiku-4-5 | Fast extraction — fetches source content, strips boilerplate, extracts clean text |
| **content-analyzer** | claude-sonnet-4-6 | Deep analysis — identifies key themes, quotable passages, stats, CTAs, audience segments |
| **variant-writer** | claude-sonnet-4-6 | Creative adaptation — writes all platform-specific content variants with proper tone/format |
| **quality-reviewer** | claude-opus-4-6 | Quality gate — checks faithfulness, platform fit, tone accuracy; decides publish/revise |

## Phase Pipeline

```
ingest-content → analyze-content → generate-variants → review-quality ──┐
                                        ↑                               │
                                        └────────── rework ─────────────┘
                                                                        │
                                                                   finalize-output
```

## Phase Details

### 1. ingest-content (agent: content-ingester)
**What:** Fetch long-form content from a URL (via fetch MCP) or read from local file (via filesystem MCP). Strip HTML boilerplate, navigation, ads, and sidebars. Extract clean article text, title, author, publication date, and word count.
**Input:** `config/sources.json` — list of URLs or local file paths to repurpose
**Output:** `data/source-content.json` — cleaned source text with metadata (title, author, date, word count, URL)
**Decision:** verdict = `has-content` | `no-content` (terminates if source is empty or inaccessible)

### 2. analyze-content (agent: content-analyzer)
**What:** Deep analysis of the source content using sequential-thinking MCP. Identify: 3-5 key themes, top quotable passages (with attribution), statistics/data points, calls-to-action, target audience segments, content tone (formal/casual/technical), and a one-line hook for each platform.
**Input:** `data/source-content.json`
**Output:** `data/content-analysis.json` — structured analysis with themes, quotes, stats, hooks, audience info
**MCP:** sequential-thinking for structured reasoning about content strategy

### 3. generate-variants (agent: variant-writer)
**What:** Generate platform-specific content variants. Each variant follows platform conventions:
- **Twitter/X thread:** 5-12 tweets, hook-first, use line breaks for readability, end with CTA. Max 280 chars per tweet.
- **LinkedIn post:** Professional tone, 1300 chars max, use line breaks and whitespace, 3-5 hashtags, storytelling format.
- **Email newsletter snippet:** Subject line + preview text + 150-word body + CTA button text. Warm, direct tone.
- **Instagram caption:** Conversational, emoji-friendly, 2200 chars max, 20-30 hashtags in comment block, strong opening line.
- **YouTube description:** SEO-optimized, timestamps placeholder, links section, 5000 chars max, key takeaways as bullet list.

If `data/review-feedback.json` exists (rework loop), address all feedback items.
**Input:** `data/content-analysis.json` (+ `data/review-feedback.json` if rework)
**Output:** `output/variants/twitter-thread.md`, `output/variants/linkedin-post.md`, `output/variants/email-snippet.md`, `output/variants/instagram-caption.md`, `output/variants/youtube-description.md`, `output/variants/manifest.json` (summary of all variants with char counts)

### 4. review-quality (agent: quality-reviewer)
**What:** Quality gate — reviews all generated variants against the source content. Checks:
- Faithfulness: no hallucinated facts, stats, or quotes
- Platform fit: each variant meets length constraints and tone expectations
- Consistency: key message is coherent across all platforms
- Engagement: hooks are compelling, CTAs are clear
Uses sequential-thinking for thorough multi-dimensional evaluation.
**Input:** `data/source-content.json`, `data/content-analysis.json`, `output/variants/`
**Output:** `data/review-feedback.json` (if rework), `data/review-report.md` (always)
**Decision:** verdict = `publish` | `rework` | `reject`
  - `publish` → proceed to finalize
  - `rework` → loop back to generate-variants with specific feedback per variant (max 3 attempts)
  - `reject` → terminate (source content not suitable for repurposing)

### 5. finalize-output (agent: content-analyzer)
**What:** Generate a final summary report: what was repurposed, variant stats (char counts, platform readiness), suggested publication schedule (optimal posting times per platform), and a content calendar snippet. Write everything to `output/`.
**Input:** `output/variants/`, `data/content-analysis.json`, `data/review-report.md`
**Output:** `output/summary.md` — human-readable report, `output/calendar.json` — suggested publication schedule

## MCP Servers Needed

| Server | Package | Purpose |
|---|---|---|
| filesystem | `@modelcontextprotocol/server-filesystem` | Read source files, write data/output files |
| fetch | `@modelcontextprotocol/server-fetch` | Fetch content from URLs (blog posts, articles) |
| sequential-thinking | `@modelcontextprotocol/server-sequential-thinking` | Content strategy analysis and quality review reasoning |

## Directory Structure

```
content-repurposer/
├── .ao/workflows/
│   ├── agents.yaml
│   ├── phases.yaml
│   ├── workflows.yaml
│   ├── mcp-servers.yaml
│   └── schedules.yaml
├── config/
│   └── sources.json            # URLs or file paths to repurpose
├── data/                       # Runtime intermediate files
│   └── .gitkeep
├── output/                     # Final artifacts
│   ├── variants/               # Platform-specific content variants
│   │   └── .gitkeep
│   └── .gitkeep
├── templates/
│   ├── twitter-thread.md       # Template with platform constraints
│   ├── linkedin-post.md
│   ├── email-snippet.md
│   ├── instagram-caption.md
│   └── youtube-description.md
├── sample-data/
│   └── sample-blog-post.md     # Sample long-form content for demo
├── CLAUDE.md
└── README.md
```

## Workflow Features Demonstrated

- **Multi-agent pipeline** — 4 agents with distinct specializations (haiku for speed, sonnet for creative, opus for quality)
- **Multi-model routing** — Haiku for fast ingestion, Sonnet for creative writing, Opus for quality judgment
- **Decision contracts** — ingester decides if content exists; reviewer gates publication quality
- **Rework loops** — reviewer can send back to variant generation with per-variant feedback (max 3 attempts)
- **Output contracts** — structured variants per platform with manifest, content calendar, summary report
- **Phase routing** — early termination on no-content or reject, rework on quality failure
- **Template-driven output** — platform templates enforce constraints (char limits, formatting rules)
- **Fetch MCP** — real URL ingestion of blog posts and articles from the web
- **Sequential-thinking MCP** — structured reasoning for content strategy and quality evaluation
- **Scheduled runs** — daily check for new source content to repurpose

## Sample Input (config/sources.json)

```json
{
  "sources": [
    {
      "type": "url",
      "url": "https://blog.example.com/our-latest-product-update",
      "title": "Product Update Blog Post",
      "priority": "high"
    },
    {
      "type": "file",
      "path": "sample-data/sample-blog-post.md",
      "title": "Sample Blog Post",
      "priority": "normal"
    }
  ],
  "platforms": ["twitter", "linkedin", "email", "instagram", "youtube"],
  "brand_voice": {
    "tone": "professional but approachable",
    "audience": "B2B SaaS professionals",
    "avoid": ["jargon", "clickbait", "excessive emojis"]
  }
}
```

## Sample Output (output/variants/manifest.json)

```json
{
  "source": {
    "title": "Our Latest Product Update",
    "url": "https://blog.example.com/our-latest-product-update",
    "word_count": 1847,
    "ingested_at": "2026-03-31T14:00:00Z"
  },
  "variants": [
    {
      "platform": "twitter",
      "file": "twitter-thread.md",
      "tweet_count": 8,
      "total_chars": 1920,
      "status": "ready"
    },
    {
      "platform": "linkedin",
      "file": "linkedin-post.md",
      "char_count": 1180,
      "status": "ready"
    },
    {
      "platform": "email",
      "file": "email-snippet.md",
      "word_count": 142,
      "status": "ready"
    },
    {
      "platform": "instagram",
      "file": "instagram-caption.md",
      "char_count": 1850,
      "hashtag_count": 25,
      "status": "ready"
    },
    {
      "platform": "youtube",
      "file": "youtube-description.md",
      "char_count": 3200,
      "status": "ready"
    }
  ],
  "review": {
    "verdict": "publish",
    "faithfulness_score": 0.95,
    "attempts": 1
  }
}
```

## Sample Output (output/summary.md)

```markdown
# Content Repurposing Report

**Source:** Our Latest Product Update
**URL:** https://blog.example.com/our-latest-product-update
**Word Count:** 1,847 words | **Processed:** 2026-03-31

## Key Themes Extracted
1. New collaboration features for remote teams
2. Performance improvements (3x faster load times)
3. Enterprise security certifications achieved

## Variants Generated

| Platform | File | Status | Key Metric |
|---|---|---|---|
| Twitter/X | twitter-thread.md | Ready | 8 tweets |
| LinkedIn | linkedin-post.md | Ready | 1,180 chars |
| Email | email-snippet.md | Ready | 142 words |
| Instagram | instagram-caption.md | Ready | 25 hashtags |
| YouTube | youtube-description.md | Ready | 3,200 chars |

## Suggested Publication Schedule

| Platform | Best Time | Day |
|---|---|---|
| Twitter/X | 9:00 AM ET | Tuesday |
| LinkedIn | 10:00 AM ET | Tuesday |
| Email | 8:00 AM ET | Wednesday |
| Instagram | 12:00 PM ET | Wednesday |
| YouTube | 2:00 PM ET | Thursday |

## Quality Review
- Faithfulness: 95% — all facts verified against source
- Attempt: 1 of 3 (passed first review)
```
