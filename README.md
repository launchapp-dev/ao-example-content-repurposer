# Content Repurposing Pipeline

Automatically transforms long-form content (blog posts, articles, whitepapers) into platform-native variants for Twitter/X, LinkedIn, Email, Instagram, and YouTube — with a quality gate that catches hallucinations and platform-fit issues before you publish.

## Workflow Diagram

```
                    ┌─────────────────────────────────────────────┐
                    │              repurpose-content               │
                    └─────────────────────────────────────────────┘

  config/sources.json
         │
         ▼
  ┌──────────────┐    no-content    ┌──────────────────────────┐
  │ingest-content│ ───────────────► │  TERMINATE (no content)  │
  └──────────────┘                  └──────────────────────────┘
         │ has-content
         ▼
  ┌───────────────┐
  │analyze-content│  (sequential-thinking MCP: themes, quotes, hooks)
  └───────────────┘
         │
         ▼
  ┌────────────────┐◄──────────────────────────────┐
  │generate-variants│                              │ rework
  └────────────────┘                              │ (with per-variant feedback)
         │                                        │
         ▼                                        │
  ┌──────────────┐    reject        ┌─────────────────────────┐
  │review-quality│ ───────────────► │  TERMINATE (unsuitable) │
  └──────────────┘                  └─────────────────────────┘
         │ publish
         ▼
  ┌───────────────┐
  │finalize-output│  (summary report + publication calendar)
  └───────────────┘
         │
         ▼
  output/summary.md
  output/calendar.json
  output/variants/*.md
```

## Quick Start

```bash
cd examples/content-repurposer

# Edit config/sources.json to add your content URL or local file path
# Then start the daemon:
ao daemon start

# Run the repurposing workflow:
ao workflow run repurpose-content

# Watch live progress:
ao daemon stream --pretty
```

## Agents

| Agent | Model | Role |
|---|---|---|
| **content-ingester** | claude-haiku-4-5 | Fetches URLs or reads local files, strips boilerplate, extracts clean text |
| **content-analyzer** | claude-sonnet-4-6 | Extracts themes, quotes, stats, and per-platform hooks; writes final summary report |
| **variant-writer** | claude-sonnet-4-6 | Writes all 5 platform-specific content variants with proper tone and constraints |
| **quality-reviewer** | claude-opus-4-6 | Quality gates variants for faithfulness, platform fit, consistency, and engagement |

## AO Features Demonstrated

- **Multi-agent pipeline** — 4 agents with distinct specializations running sequentially
- **Multi-model routing** — Haiku for speed (ingestion), Sonnet for creative writing, Opus for quality judgment
- **Decision contracts** — ingester decides `has-content` / `no-content`; reviewer decides `publish` / `rework` / `reject`
- **Rework loops** — reviewer sends back to `generate-variants` with per-platform feedback (max 3 attempts)
- **Phase routing** — early termination on no-content or reject verdicts
- **Fetch MCP** — real URL ingestion of web content without any API keys
- **Sequential-thinking MCP** — structured multi-step reasoning for content strategy and quality evaluation
- **Output contracts** — structured variant files per platform + manifest + publication calendar
- **Scheduled runs** — optional daily trigger for batch processing new content (weekdays 8am)

## Requirements

### No API keys required for the demo
The sample input uses a local file (`sample-data/sample-blog-post.md`). The pipeline runs fully on Claude.

### To repurpose content from a URL
No additional setup needed — the `mcp-fetch-server` MCP fetches any public URL.

Edit `config/sources.json` and set `"type": "url"` with your URL.

### MCP Servers (auto-installed via npx)
- `@modelcontextprotocol/server-filesystem` — file read/write
- `mcp-fetch-server` — URL content fetching
- `@modelcontextprotocol/server-sequential-thinking` — structured reasoning

### AO Daemon
```bash
ao daemon start --autonomous
```

## Output Structure

```
output/
├── summary.md              # Full repurposing report with publication schedule
├── calendar.json           # Machine-readable publication schedule
└── variants/
    ├── manifest.json       # Summary of all variants with char counts and status
    ├── twitter-thread.md   # 5-12 tweet thread
    ├── linkedin-post.md    # Professional post (≤1,300 chars)
    ├── email-snippet.md    # Subject + preview + body + CTA
    ├── instagram-caption.md # Conversational caption + hashtag block
    └── youtube-description.md # SEO-optimized description with timestamps
```

## Configuration

Edit `config/sources.json` to customize:
- **sources** — list of URLs or local file paths to process
- **platforms** — which platforms to generate variants for
- **brand_voice** — tone, target audience, and words to avoid
