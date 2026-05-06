# Scoring Algorithm — Quality-First Content Ranking

**Method:** Multi-Criteria Decision Analysis (MCDA) with Analytic Trait Rubrics
**Purpose:** Rank content for a time-constrained learner where only the best material gets through.
**Created:** 18 March 2026

---

## The Core Insight

Most content ranking systems use **single-factor scoring** per category — one number per dimension, decided by one signal (usually engagement metrics like views or likes). This is fast to compute but produces poor rankings because it conflates popularity with quality.

This algorithm uses **analytic trait rubrics** instead: each scoring category has a maximum cap, but the score within that cap is composed of multiple independent traits. No single trait can max out the category. This forces the scoring to reward *breadth of quality signals* rather than one dominant metric.

**Example:** Quality Signal has a 35-point cap. A YouTube video with 1 million views but no original thinking, no framework, and no specific claims scores only 4–8 points. A 30K-view video by a practitioner that introduces a novel framework, names real systems, and bridges two disciplines scores 26–31 points. The method values what the content *does*, not how many people saw it.

---

## Why This Works

Three design principles make this effective:

**1. Quality dominates through weight allocation.** Quality Signal carries 35% of the total score — the single largest dimension. But because it's measured through 6 independent traits rather than one metric, it's hard to "hack." Content must be genuinely good across multiple dimensions to score high. A viral hot take maxes out at ~8/35 on quality no matter how many views it has.

**2. A hard gate prevents irrelevant content from scoring at all.** Before any scoring happens, content must pass a topic relevance gate. This is binary: either the content matches a keyword from the master topic list, or it's excluded with a score of zero. This eliminates ~60% of content from high-volume feeds before the expensive scoring step runs.

**3. Recency is a tiebreaker, not a gatekeeper.** Most recommendation systems over-weight recency because it's easy to measure. But for deep learning, a 12-month-old landmark essay is worth more than yesterday's hot take. This algorithm gives recency only 5 points out of 100 — enough to break ties, never enough to promote shallow content over deep content.

---

## The Method — Named Components

| Component | Formal name | Plain language |
|-----------|-------------|----------------|
| Overall approach | **Multi-Criteria Decision Analysis (MCDA)** | Score content across several independent dimensions, then combine into a single number for ranking. |
| Combination method | **Weighted Sum Model (WSM)** | Each dimension gets a weight reflecting its importance. Final score = sum of (dimension score x weight). Simple, transparent, explainable. |
| Within-dimension scoring | **Analytic Trait Rubric** | Instead of scoring each dimension with one holistic judgement, break it into specific observable traits. Each trait contributes points independently up to the category cap. |
| Pre-scoring filter | **Sequential Screening (Funnel)** | Apply cheap pass/fail filters before expensive scoring. Hard filters (age, duplicates) -> topic gate -> full scoring. |
| Serendipity mechanism | **Controlled Randomness (Wild Card)** | One slot per cycle is exempt from the normal threshold, allowing surprising content to surface — but still quality-gated. |

---

## Algorithm Structure

### Stage 0 — Hard Filters (instant exclude)

| Filter | Rule |
|--------|------|
| Age | Older than 18 months -> exclude |
| Previously recommended | URL in `previously-recommended.md` -> exclude |
| Already in plan | URL in learning plan Weekly Checklist -> exclude |

### Stage 1 — Topic Gate (pass/fail)

Content must match at least one keyword from the master topic list OR match the current learning week's focus. No match -> score = 0, skip.

**Master keyword list:** agents, MCP, trust, HCI, agentic UX, Copilot, long-running agents, human-in-the-loop, AI interaction patterns, evaluation, safety, orchestration, design systems for AI, model behaviour, alignment, prompt engineering, multimodal, co-intelligence

### Stage 2 — Quality-First Scoring (0–100)

```
Score = Quality_Signal(35) + Topic_Relevance(25) + Content_Depth(20) + Source_Trust(15) + Recency_Bonus(5)
```

---

### Quality Signal (0–35) — Analytic Trait Rubric

The most important dimension. Scored by the presence of independent content traits, not by engagement metrics.

| Trait | Points | What the agent looks for |
|-------|--------|--------------------------|
| Makes a specific, falsifiable claim | +8 | Named constraint, concrete number, specific position — not "AI will change everything" |
| Shows original work | +8 | Novel framework, architecture diagram, code, design artefact, or research finding |
| Names real products or systems | +5 | Mentions Claude, Copilot, MCP, LangChain by name — not generic "AI tools" |
| Written/presented by a practitioner | +5 | Author builds or ships, not just comments or analyses |
| Cross-domain connection | +5 | Bridges design <-> engineering <-> product <-> AI theory |
| Referenced by a Tier 1 voice | +4 | A Tier 1 source on the curated list has shared or cited this content |

**Maximum: 35.** Most good content hits 3–4 traits (18–26 points). Hitting all 6 is exceptional.

**Engagement as tiebreaker only:** If two pieces score identically on traits, higher engagement breaks the tie. Engagement never adds to the score directly.

**Why this works better than view counts:** A 500K-view clickbait video with no original thinking scores 4–8/35. A 3K-view podcast episode where a practitioner introduces a novel trust framework and names specific systems scores 21–26/35. The method rewards substance over visibility.

---

### Topic Relevance (0–25)

| Match type | Points |
|------------|--------|
| Direct match to current week's **primary topic** | 25 |
| Match to current week's **secondary topics** | 18 |
| Match to **master keyword list** | 12 |
| General AI interest, passed gate but no specific keyword | 6 |

---

### Content Depth (0–20)

Measures substance, not length. Length is a secondary signal.

| Score | Level | What it looks like |
|-------|-------|--------------------|
| 16–20 | Deep | Introduces a framework, shows trade-offs, includes evidence, addresses counterarguments. Reader leaves with an applicable mental model. |
| 11–15 | Substantial | Explains a concept well with examples. Teaches something specific and new. |
| 6–10 | Moderate | Competent summary or walkthrough. Useful but doesn't push thinking forward. |
| 1–5 | Shallow | Hot take, announcement summary, reaction content. Nothing retained. |

**Length modifier (adjusts within depth score, doesn't determine it):**
- Long-form (>2000 words / >30 min video): +3
- Short-form (<500 words / <5 min video): -2

---

### Source Trust (0–15)

Stable score based on the curated source list.

| Source | Points |
|--------|--------|
| Tier 1 curated source | 15 |
| Tier 2 curated source | 10 |
| Discovered (not on curated list) | 5 |

Discovered sources face a 10-point trust gap versus Tier 1. They must compensate with higher quality + relevance to surface. This is intentional — the curated list exists because those sources have been vetted.

---

### Recency Bonus (0–5)

Gentle tiebreaker. Never dominant.

| Age | Points |
|-----|--------|
| Last 7 days | 5 |
| 8–30 days | 3 |
| 1–6 months | 2 |
| 6–18 months | 1 |
| >18 months | EXCLUDED |

**Why only 5 points:** A 15-month-old Karpathy video (Quality: 31 + Topic: 25 + Depth: 18 + Trust: 15 + Recency: 1 = 90) must outrank a 2-day-old tweet (Quality: 5 + Topic: 6 + Depth: 3 + Trust: 5 + Recency: 5 = 24). It does.

---

## Output Tiers

| Tier | Threshold | Volume | Action |
|------|-----------|--------|--------|
| **Must Read** | Score >= 75 | 1–3 items/week | Surface with a one-sentence reason |
| **Recommended** | Score 50–74 | 3–5 items/week | Surface with a one-line description |
| **Wild Card** | Score >= 35 + bonus trigger | 1 item/week | Labelled "Wild Card" with reason |
| **Dropped** | Score < 50 | — | Logged for dedup, not surfaced |

**Fallback rule:** If fewer than 1 item scores >= 75, promote the highest-scoring item to Must Read. There's always something worth reading.

---

## The Wild Card — Structured Serendipity

Once per week, the agent surfaces one item that wouldn't normally make the cut. This prevents the filter from becoming a bubble.

**Rules:**
- Must pass the topic gate (minimum relevance)
- Must score >= 20 on Quality Signal (still has to be good)
- Threshold drops to >= 35 total (vs >= 50 for Recommended)
- Must trigger at least one bonus:

| Bonus trigger | What it means |
|---------------|---------------|
| New voice | Source Katherine has never seen before |
| Unusual cross-domain connection | Bridges an unexpected discipline (philosophy + AI, neuroscience + agent design) |
| Contrarian position | Contradicts mainstream consensus with substance |
| Creative approach | Genuinely playful, weird, or inventive take |

---

## Why This Method Is Better Than Single-Factor Scoring

| Approach | Weakness | This algorithm's fix |
|----------|----------|----------------------|
| Score by view counts / likes | Rewards virality, not quality. Clickbait scores high. | Quality scored by 6 content traits. Engagement is tiebreaker only. |
| Score by single holistic judgement | Subjective, inconsistent, hard to debug. | Analytic rubric: score each observable trait independently, sum to category cap. |
| Weight all dimensions equally | Topic-irrelevant content competes with relevant content. | Topic gate eliminates irrelevant content before scoring begins. |
| Over-weight recency | Shallow breaking news outranks deep essays. | Recency is 5/100 — a tiebreaker, not a driver. |
| No serendipity mechanism | Filter becomes a bubble. Same voices, same topics. | Wild card slot: one surprising item per week, still quality-gated. |

---

## Implementation Notes

- The topic gate and hard filters run first (cheap, eliminates most content)
- Quality Signal trait detection uses title + description + first 500 characters of content. Full content analysis only needed for borderline scores (40–55 range).
- Source Trust is a static lookup from the curated source list — compute once, cache.
- `previously-recommended.md` grows over time. Agent reads it on each run and appends new recommendations after each briefing.
- Wild Card selection: after main scoring, scan all items scoring 35–49 for bonus triggers. Pick the one with the highest Quality Signal sub-score.

---

*Methodology documented 18 March 2026. Algorithm v1.*
