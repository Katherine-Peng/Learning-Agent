# Learning Partner Agent

An AI-powered weekly content curator that scans 49 sources, scores content using a multi-criteria algorithm, and delivers a ranked briefing to Notion every Sunday evening.

Built for a 26-week AI learning plan. The agent finds the highest-quality content for each week's topic — whether it was published yesterday or 15 months ago. A brilliant year-old blog post outranks a mediocre new tweet.

## How it works

```
Every Sunday at 7pm
        |
        v
+-------------------+     +-------------------+     +-------------------+
|  Read context      | --> |  Scan sources     | --> |  Score & rank     |
|  - Weekly topic    |     |  - 13 YouTube RSS |     |  - MCDA scoring   |
|  - Source list     |     |  - 18 blog feeds  |     |  - 0-100 scale    |
|  - Already-read    |     |  - Web search     |     |  - Hard cap: 7    |
+-------------------+     +-------------------+     +-------------------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   |  Write briefing   |
                                                   |  to Notion        |
                                                   +-------------------+
```

**No Python runtime.** The entire agent is a structured prompt that Claude executes via [Claude Code](https://docs.anthropic.com/en/docs/claude-code) scheduled tasks. It reads config files, fetches RSS feeds, applies scoring logic, and writes to Notion — all through tool use.

### Architecture

1. **Prompt-driven** — The agent's behaviour lives in [`prompts/weekly-briefing.md`](prompts/weekly-briefing.md). No application code.
2. **Week-aware** — Reads the Notion checklist to detect the current week and its topic automatically.
3. **Two-scan approach** — Fresh scan (last 7 days via RSS) + evergreen scan (best of 18 months via web search).
4. **Quality over quantity** — Scores every candidate on 5 dimensions, hard-caps output at 7 items.
5. **Dedup across runs** — Tracks all previously recommended URLs to avoid repeats.

## Repo structure

```
Learning Agent/
├── README.md                          # This file
├── prompts/
│   └── weekly-briefing.md             # The agent prompt (source of truth)
├── config/
│   ├── sources.md                     # 49 curated sources with feed URLs
│   ├── scoring-algorithm.md           # Full MCDA scoring spec
│   └── previously-recommended.md      # Dedup log (appended each run)
└── logs/
    ├── run-2026-03-19-run2.md         # Run logs with stats
    ├── run-2026-03-23.md
    ├── run-2026-03-29.md
    ├── run-2026-04-07.md
    └── run-2026-05-03.md
```

Briefings are written directly to Notion (not stored locally).

## Scoring algorithm

Each piece of content is scored 0–100 across five dimensions:

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| **Quality Signal** | 0–35 | 6 independent traits: falsifiable claims, original work, named systems, practitioner author, cross-domain, Tier 1 reference |
| **Topic Relevance** | 0–25 | Match to current week's topic vs. general AI interest |
| **Content Depth** | 0–20 | Framework-level thinking vs. hot takes |
| **Source Trust** | 0–15 | Tier 1 (15) > Tier 2 (10) > Discovered (5) |
| **Recency Bonus** | 0–5 | Gentle tiebreaker — never enough to promote shallow content |

Items scoring 75+ become **Must Read**. Items 50–74 become **Recommended**. One **Wild Card** slot surfaces surprising content that wouldn't normally make the cut (contrarian takes, new voices, cross-domain connections).

Full specification: [`config/scoring-algorithm.md`](config/scoring-algorithm.md)

## Example output

Below is a real briefing the agent generated for Week 7 (Context Engineering + RAG) on 3 May 2026. This is what gets delivered to Notion each week.

---

> **Generated:** 2026-05-03 | **Focus:** Context engineering + RAG
> **Sources checked:** YouTube (13 channels), Blogs/RSS (18 feeds), Web (5 searches)
> **Estimated total reading time:** ~2 hrs 27 min
>
> ### ⭐ Must Read
>
> **1. Everything You Need to Know About Context Engineering in 40 Minutes**
> **Ravi Mehta (via Peter Yang)** · YouTube · ~40 min watch · Score: 90/100
> https://www.youtube.com/watch?v=wUWljYoQN8g
>
> **Summary:** Ravi Mehta presents a structured 3-layer system for context engineering: functional context (task instructions and examples), visual context (layout and format signals), and data context (retrieved information via RAG and memory). He shows how most failures in production AI aren't model problems — they're context design problems.
>
> **Why it matters for Week 7:** Context engineering is this week's named topic — and this video gives you a working framework before you've fully absorbed RAG and vector databases. Mehta's 3-layer model maps cleanly to design decisions you'll face when working on Copilot features.
>
> ---
>
> **2. Designing Stable Interfaces For Streaming Content**
> **Joas Pambou** · Smashing Magazine · ~19 min read · Score: 88/100
> https://smashingmagazine.com/2026/05/designing-stable-interfaces-streaming-content/
>
> **Summary:** Addresses three failure modes in AI streaming interfaces: scroll hijacking, layout shifts, and render degradation. Proposes concrete design-engineering principles including user-controlled scroll, incremental DOM updates, and batched rendering.
>
> **Why it matters for Week 7:** As you learn about RAG — which delivers context in real-time chunks — this teaches the UX layer that sits on top: how to design an interface that stays stable while AI is generating.
>
> ### 🟠 Recommended
>
> **3. Red-Teaming a Network of Agents** · Microsoft Research · ~15 min read · Score: 85/100
>
> **4. Why Cultivating Agency Matters More Than Skills** · Lenny's Podcast · ~60 min watch · Score: 77/100
>
> **5. Context Engineering** · Simon Willison · ~3 min read · Score: 76/100
>
> ### 🃏 Wild Card
>
> **6. The Web Trained AI to Deceive — Now Designers Have to Untrain It** · UX Collective · ~10 min read · Score: 66/100
>
> Contrarian reframing: AI deception isn't a model problem — AI absorbed dark patterns from the web's training data. Designers are implicated in the problem and responsible for the fix.
>
> ### ✅ Checklist
> - [ ] ⭐ Everything You Need to Know About Context Engineering in 40 Minutes · ~40 min watch
> - [ ] ⭐ Designing Stable Interfaces For Streaming Content · ~19 min read
> - [ ] 🟠 Red-Teaming a Network of Agents · ~15 min read
> - [ ] 🟠 Why Cultivating Agency Matters More Than Skills · ~60 min watch
> - [ ] 🟠 Context Engineering (Simon Willison) · ~3 min read
> - [ ] 🃏 The Web Trained AI to Deceive · ~10 min read

---

## Setup

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- Notion MCP server connected (for reading the weekly checklist and writing briefings)
- A Notion workspace with the weekly checklist and briefing parent pages

### Scheduled tasks

The agent runs as two Claude Code scheduled tasks:

| Task | Schedule | Purpose |
|------|----------|---------|
| `weekly-learning-briefing` | Sunday 7pm (`0 19 * * 0`) | Primary weekly run |
| `weekly-learning-briefing-retry` | Sunday 9pm (`0 21 * * 0`) | Backup if primary fails (checks for existing run log first) |

Both tasks have the full prompt inlined. To update agent behaviour, edit `prompts/weekly-briefing.md` and push the changes to both scheduled tasks via `update_scheduled_task`.

### Configuration

- **Add/remove sources:** Edit `config/sources.md` (and update the Notion Master Source Table)
- **Adjust scoring:** Edit `config/scoring-algorithm.md` and update the corresponding section in `prompts/weekly-briefing.md`
- **Change briefing format:** Edit the Step 4 template in `prompts/weekly-briefing.md`

## Sources

The agent monitors 49 curated sources across 6 categories:

| Category | Examples | Count |
|----------|----------|-------|
| Design x AI | Google PAIR, Microsoft HAX, Maggie Appleton, Vitaly Friedman | 8 |
| AI Engineering | Anthropic, Simon Willison, Karpathy, LangChain | 10 |
| Research | Stanford HAI, DeepMind, Microsoft Research | 5 |
| Product | Lenny's Podcast, Ethan Mollick, Peter Yang | 5 |
| Strategy | Dwarkesh Patel, Ben Thompson, No Priors | 4 |
| Safety / Critique | Normal Tech, Robert Miles, Gary Marcus, Nathan Lambert | 5 |
| + 12 more | YouTube channels, design blogs, engineering substacks | 12 |

Full list: [`config/sources.md`](config/sources.md)
