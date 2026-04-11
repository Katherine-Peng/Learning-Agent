# Agent Briefing — Week 2 — 2026-03-19 (Run 2)

## Weekly Briefing — Week 2
**Generated:** 2026-03-19 (second run of the day) | **Focus:** Attention, transformers + how LLMs work
**Sources checked:** 20 RSS/feed sources, 3 web searches
**Items collected:** ~35 | **After filters:** 18 | **Recommended:** 9

> **Note:** An earlier run today (briefing-2026-03-19.md) found excellent content but could not write to Notion (tools unavailable). This run finds fresh content from the remaining source list, with all previously-recommended URLs filtered out. Notion tools remain unavailable. Briefing saved locally again — please set up the Notion MCP connector to enable automatic posting.

---

## ⭐ Must Read

### 1. Autoresearching Apple's "LLM in a Flash"
**Simon Willison** · simonwillison.net · Blog post + paper · ~8 min read · Score: 85/100
https://simonwillison.net/2026/Mar/18/llm-in-a-flash/

**Summary:** Dan Woods used Claude Code to run 90 autonomous experiments applying Apple's "LLM in a Flash" technique — storing a 397-billion parameter model on SSD rather than RAM, loading weights on demand. The result: a quantized 397B model running at 5.5+ tokens/second on a MacBook Pro with 48GB RAM. Claude Opus 4.6 didn't just assist — it generated optimized MLX Objective-C and Metal code and authored most of the resulting research paper. The technique works especially well with mixture-of-experts (MoE) architectures, where each token only activates a subset of expert weights, allowing selective streaming rather than loading everything at once.

**Why it matters for Week 2:** This story operates on two levels simultaneously. Level one is Week 2 content: the flash technique only makes sense if you understand how transformer inference works — which weights get loaded, when, why MoE sparsity matters, what the bottleneck is between flash storage and DRAM. If you've done the Karpathy video (from Run 1), this is its practical extension. Level two is a preview of Weeks 5–10: Claude running 90 experiments, generating code, and writing a paper is exactly what "agentic AI" looks like when it works. The two levels are inseparable: you can only build agents that reason about LLMs if you understand what LLMs are doing internally.

---

### 2. A Dream of Spring for Open-Weight LLMs: 10 Architectures from Jan–Feb 2026
**Sebastian Raschka** · magazine.sebastianraschka.com · Substack · ~20 min read · Score: 84/100
https://magazine.sebastianraschka.com/p/a-dream-of-spring-for-open-weight

**Summary:** A comprehensive technical survey of ten open-weight LLM architectures released in January and February 2026 — from Arcee AI's Trinity Large (400B) to Cohere's Tiny Aya (3.35B), with stops at MiniMax M2.5, z.AI's GLM-5, Moonshot's Kimi K2.5 (1T parameters), and others. The headline finding: the field is converging on hybrid attention mechanisms — combining standard multi-head attention with linear alternatives like Gated DeltaNet (Qwen3.5) or Lightning Attention (Ling 2.5) — and on techniques like Multi-Head Latent Attention (MLA) to reduce KV cache overhead. The counterintuitive insight: "modeling performance is likely not attributed to the architecture design itself but rather the dataset quality and training recipes." MiniMax M2.5's decision to stick with classical Grouped Query Attention rather than a hybrid approach, while remaining competitive, supports this.

**Why it matters for Week 2:** The Karpathy video teaches you the canonical transformer. This article shows you what practitioners are doing with and to that canonical design right now. The hybrid trend is crucial Week 2 knowledge: the field isn't abandoning transformers, it's evolving them by combining attention's global context-tracking with linear alternatives' efficiency. Reading this article after Karpathy's deep dive will sharpen your ability to distinguish between "how transformers work" and "what specific architectural choices matter and why." It also surfaces the dataset-quality-over-architecture insight, which will inform how you think about what makes models actually different from each other.

---

### 3. OLMo Hybrid and Future LLM Architectures
**Nathan Lambert** · interconnects.ai · Substack · ~15 min read · Score: 77/100
https://www.interconnects.ai/p/olmo-hybrid-and-future-llm-architectures

**Summary:** Nathan Lambert covers the release of OLMo Hybrid, a 7B base model combining recurrent neural network (RNN) modules with traditional attention in a 3:1 ratio. The theoretical claim is significant: hybrid architectures are "more powerful than the sum of their parts" — they can express certain computational functions that neither pure transformers nor pure RNNs can represent alone. Scaling experiments show approximately 2X training efficiency gains over pure transformers during pretraining. The practical catch: current open-source inference tools lack optimized kernels for hybrid architectures, meaning the speed advantages disappear in production deployment until tooling catches up. Lambert argues this is likely what several frontier labs are quietly exploring, but open-source adoption is blocked by infrastructure.

**Why it matters for Week 2:** OLMo Hybrid is the clearest illustration this week of transformer architecture at a transition point. You're learning attention mechanisms and transformer internals precisely when the field is asking: what should replace or augment them? The 2X training efficiency finding is concrete evidence that the pure-attention transformer is not the final architecture. The tooling gap is equally educational — it shows that having better theoretical properties doesn't automatically translate to deployment advantage. This is exactly the kind of engineering trade-off reasoning Week 2 is trying to build.

---

## 🟠 Recommended

### 4. ImportAI 449: LLMs Training Other LLMs; 72B Distributed Training Run; Computer Vision Is Harder Than Generative Text
**Jack Clark** · jack-clark.net · Newsletter · ~12 min read · Score: 73/100
https://jack-clark.net/2026/03/16/importai-449-llms-training-other-llms-72b-distributed-training-run-computer-vision-is-harder-than-generative-text/

**One-liner:** PostTrainBench shows AI agents can now automate post-training at 23.2% of human performance (vs 9.9% six months ago) — the closing gap is the data point to track; the Covenant-72B distributed training story and the computer vision harder-than-text finding are the other two threads worth 15 minutes.

---

### 5. Systematic Debugging for AI Agents: Introducing the AgentRx Framework
**Microsoft Research** · Research blog · ~10 min read · Score: 72/100
https://www.microsoft.com/en-us/research/blog/systematic-debugging-for-ai-agents-introducing-the-agentrx-framework/

**One-liner:** AgentRx automatically generates executable constraints and auditable logs to pinpoint where multi-step agent workflows fail — a diagnostic layer for production agents that doesn't yet exist in most toolchains.

---

### 6. The Anatomy of an Agent Harness
**LangChain** · blog.langchain.com · Blog · ~10 min read · Score: 70/100
https://blog.langchain.com/the-anatomy-of-an-agent-harness/

**One-liner:** "The model contains the intelligence and the harness is the system that makes that intelligence useful" — this post maps the six components of an agent harness (filesystem, code execution sandbox, memory, context management, planning loops, verification) into a vocabulary you'll use every time you build or evaluate an agent system.

---

### 7. GPT 5.4 Is a Big Step for Codex
**Nathan Lambert** · interconnects.ai · Substack · ~10 min read · Score: 70/100
https://www.interconnects.ai/p/gpt-54-is-a-big-step-for-codex

**One-liner:** Lambert's side-by-side mechanical analysis of GPT 5.4 vs Claude in Codex is useful less for the conclusion ("GPT 5.4 feels like a step in all four traits: correctness, speed, cost, DX") and more for the framework it gives you to evaluate any two coding agent models against each other.

---

### 8. Applying Statistics to LLM Evaluations
**Cameron Wolfe** · cameronrwolfe.substack.com · Substack · ~15 min read · Score: 69/100
https://cameronrwolfe.substack.com/p/stats-llm-evals

**One-liner:** The most practical Week 2 supplement — once you understand how LLMs generate outputs, the next question is how to measure them rigorously; this post on confidence intervals, standard error, and hypothesis testing for LLM evals will prevent you from being fooled by noise when you start running your own model comparisons.

---

## 🔵 Discovered

### Agentic AI: Reimagining Future Human–Agent Communication and Collaboration
**Microsoft Research Asia** · StarTrack Scholars 2026 · Research article · ~8 min read
https://www.microsoft.com/en-us/research/articles/agentic-ai-reimagining-future-human-agent-communication-and-collaboration/

**Who is this:** This isn't from the standard Microsoft Research Blog feed — it's from the StarTrack Scholars program at Microsoft Research Asia, which sponsors early-career researchers. Published December 2025. It surfaces three specific research priorities for agentic human-AI collaboration: multimodal perception and persistent memory across sessions; generative, adaptive interfaces that respond to user context; and transparency and provenance tracking for responsible deployment.

**Why it matters:** This is the clearest statement from a major lab of what the design requirements for *trustworthy* agentic AI actually are — not "what can agents do" but "how should they communicate with humans to earn ongoing trust." This is Katherine's direct domain. The "sustaining memory across sessions" priority maps directly to what M365 Copilot is building; the transparency and provenance requirements map to the AI governance conversations she's in. It's a 3.5-month-old article, not breaking news — but it surfaces from a search rather than a feed, suggesting it's been undersurfaced.

---

## 🃏 Wild Card

No Wild Card this run. All qualifying items scored in the Recommended range (50–74) rather than the Wild Card threshold (35–49). Nothing was excluded — the quality bar held.

---

## 📊 Signals & Patterns

Three themes converged this week. **Transformer architecture is in transition:** all three Must Reads independently point to hybrid attention approaches as the near-term frontier — not replacements for transformers but evolutions that combine attention's global context tracking with linear alternatives' efficiency. The pure-attention transformer you learn in Week 2 is the baseline from which the field is now diverging. **AI doing AI R&D is real and accelerating:** the LLM-in-Flash story (Claude running 90 experiments autonomously) and the PostTrainBench finding (agents at 23.2% of human post-training performance, up from 9.9% in six months) both point to the same trend: AI systems are becoming participants in AI research, not just tools. **Harness engineering is becoming a named discipline:** the Agent Harness anatomy post, the AgentRx debugging framework, and the LangSmith Sandboxes announcement (from Run 1) all treat "the system around the model" as a distinct engineering concern with its own vocabulary and toolchain requirements. This is the Week 5–10 curriculum materializing.

---

## 🔧 Source Health

| Source | Status | Note |
|--------|--------|------|
| Google PAIR | ❌ FAILED | pair.withgoogle.com/feed.xml → 404. Has been broken for multiple runs. |
| Anthropic Engineering | ⚠️ STALE | Community RSS feed last updated Nov 24, 2025 — nearly 4 months stale. |
| Chip Huyen | ⚠️ No new posts | Last post January 2025. |
| Maggie Appleton | ⚠️ No new posts | No posts in 30 days. |
| Anthropic YouTube | ⚠️ No new content | Last video Feb 5, 2026. |
| Karpathy YouTube | ⚠️ No new content | No posts in 30 days. |
| Most other feeds | ✅ OK | Simon Willison, Nathan Lambert, LangChain, Jack Clark, Sebastian Raschka all active. |
| **Notion** | ❌ FAILED | MCP tools unavailable (second consecutive failed run). Briefing saved locally. |

**Action needed:** The Google PAIR 404 should be investigated — PAIR is a Tier 1 source for Katherine's domain. The Anthropic Engineering feed stale since November 2025 may need a new feed URL. Notion MCP connector needs to be configured.

---

## ✅ Checklist — Copy to Weekly Plan

### From Run 1 (briefing-2026-03-19.md):
- [ ] ⭐ Deep Dive into LLMs like ChatGPT — Andrej Karpathy · YouTube ~3h31m · https://www.youtube.com/watch?v=7xTGNNLPyMI
- [ ] ⭐ Pragmatic Summit Fireside Chat on Agentic Engineering — Simon Willison · ~45min · https://simonwillison.net/2026/Mar/14/pragmatic-summit/
- [ ] ⭐ Why Anthropic Thinks AI Should Have Its Own Computer — Latent Space · YouTube ~1h · https://www.youtube.com/watch?v=ZpZ7lFoWaT8
- [ ] 🟠 PlugMem: Raw Agent Interactions → Reusable Knowledge — Microsoft Research · ~12min · https://www.microsoft.com/en-us/research/blog/from-raw-interaction-to-reusable-knowledge-rethinking-memory-for-ai-agents/
- [ ] 🟠 Open SWE: Open-Source Framework for Internal Coding Agents — LangChain · ~8min · https://blog.langchain.com/open-swe-an-open-source-framework-for-internal-coding-agents/
- [ ] 🟠 Measuring Progress Toward AGI: A Cognitive Framework — DeepMind · ~8min · https://deepmind.google/blog/measuring-progress-toward-agi-a-cognitive-framework/
- [ ] 🟠 Inside Ramp: AI Agents Run Everything — Peter Yang · YouTube ~25min · https://www.youtube.com/watch?v=RBqT2PHWdBg
- [ ] 🟠 Designing AI Agents to Resist Prompt Injection — OpenAI · ~8min · https://openai.com/index/designing-agents-to-resist-prompt-injection
- [ ] 🔵 How Transformers Architecture Powers Modern LLMs — ByteBytego · ~10min · https://blog.bytebytego.com/p/how-transformers-architecture-powers
- [ ] 🃏 The Most Beautiful Formula Not Enough People Understand — 3Blue1Brown · YouTube ~30min · https://www.youtube.com/watch?v=fsLh-NYhOoU

### From Run 2 (this briefing):
- [ ] ⭐ Autoresearching Apple's "LLM in a Flash" — Simon Willison · ~8min · https://simonwillison.net/2026/Mar/18/llm-in-a-flash/
- [ ] ⭐ A Dream of Spring for Open-Weight LLMs: 10 Architectures — Sebastian Raschka · ~20min · https://magazine.sebastianraschka.com/p/a-dream-of-spring-for-open-weight
- [ ] ⭐ OLMo Hybrid and Future LLM Architectures — Nathan Lambert · ~15min · https://www.interconnects.ai/p/olmo-hybrid-and-future-llm-architectures
- [ ] 🟠 ImportAI 449: LLMs Training Other LLMs — Jack Clark · ~12min · https://jack-clark.net/2026/03/16/importai-449-llms-training-other-llms-72b-distributed-training-run-computer-vision-is-harder-than-generative-text/
- [ ] 🟠 AgentRx: Systematic Debugging for AI Agents — Microsoft Research · ~10min · https://www.microsoft.com/en-us/research/blog/systematic-debugging-for-ai-agents-introducing-the-agentrx-framework/
- [ ] 🟠 The Anatomy of an Agent Harness — LangChain · ~10min · https://blog.langchain.com/the-anatomy-of-an-agent-harness/
- [ ] 🟠 GPT 5.4 Is a Big Step for Codex — Nathan Lambert · ~10min · https://www.interconnects.ai/p/gpt-54-is-a-big-step-for-codex
- [ ] 🟠 Applying Statistics to LLM Evaluations — Cameron Wolfe · ~15min · https://cameronrwolfe.substack.com/p/stats-llm-evals
- [ ] 🔵 Agentic AI: Reimagining Future Human-Agent Communication — Microsoft Research Asia · ~8min · https://www.microsoft.com/en-us/research/articles/agentic-ai-reimagining-future-human-agent-communication-and-collaboration/
