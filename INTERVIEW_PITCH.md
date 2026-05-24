# SocraticAgent — Interview Pitch

## 30-Second Elevator Pitch

> "I built **SocraticAgent**, a multi-agent learning system on top of Claude Code. It replaces passive paper-reading with active derivation — a Socratic Tutor Agent guides the user one formula at a time, a Quiz Agent validates understanding with misconception-targeted questions, and a Scheduler Agent persists everything into an Obsidian knowledge graph with Ebbinghaus spaced repetition. It's basically an AI-native study OS."

---

## Why This Matters for an AI Algorithm Role

### 1. You Understand Multi-Agent Architecture

Most candidates talk about "using AI". You talk about **orchestrating multiple specialized agents**:
- **Tutor Agent**: role = knowledge transfer via guided discovery
- **Quiz Agent**: role = assessment + misconception detection
- **Scheduler Agent**: role = memory infrastructure + spaced repetition

This maps directly to:
- Multi-agent collaboration (e.g., MetaGPT, AutoGen)
- Role separation and tool use
- State management across long-horizon sessions

### 2. You Designed for the Right Problem

You didn't build "another AI summarizer". You identified the real failure mode of learning:
> "People don't fail because they can't read. They fail because reading is passive, and passive input decays exponentially."

Your solution targets **active recall** + **structured repetition** + **knowledge graph persistence** — the three pillars of durable learning.

### 3. You Ship with Real Infrastructure

- Obsidian graph as a **queryable knowledge backend** (not just notes)
- YAML frontmatter as a **learning-state database** (`mastery`, `interval_index`, `wrong_count`)
- Ebbinghaus algorithm as a **scheduling service**
- Claude Code plugin architecture as a **distribution mechanism**

This shows systems thinking, not just script-writing.

---

## Expected Interview Q&A

### Q: "Why three agents instead of one?"

**A**: "Single-agent systems collapse under context switching. When one LLM instance tries to simultaneously teach, assess, and schedule, it drifts — it starts teaching during the quiz, or quizzing during the derivation. By splitting into three specialized agents with clear role boundaries, each maintains its own prompt context and evaluation criteria. This is the same principle behind multi-agent frameworks like AutoGen, but applied to education."

### Q: "How do you know the quiz questions are good?"

**A**: "We don't test factual recall. We test **misconception boundaries**. For example, for scaled dot-product attention, the distractors are:
- 'divide by d_k' — wrong because it over-corrects
- 'divide by sqrt(d_k) to keep variance at 1' — correct
- 'no division needed' — misses the gradient-vanishing problem
- 'multiply by sqrt(d_k)' — sign error

Each wrong option maps to a real conceptual error I've seen people make. The quiz is essentially a **conceptual fuzzer**."

### Q: "What was the hardest design decision?"

**A**: "Whether to let the Tutor Agent correct the user immediately or let them struggle. We chose **struggle-first** — if the user is wrong, we give a hint question that's simpler, not the answer. This increases friction but improves retention. The data shows (anecdotally from my own use) that concepts I got wrong once and then re-derived stick 2-3x longer than concepts I was told outright."

### Q: "What's the next step?"

**A**: "Two directions. First, **retrieval-augmented learning** — when the Tutor Agent detects the user is stuck on a prerequisite concept (e.g., doesn't understand variance), it should pull the relevant `notes/concepts/variance.md` and insert a micro-lesson, rather than assuming prior knowledge. Second, **inter-agent communication** — the Quiz Agent's wrong-answer data should feed back to the Tutor Agent to adjust teaching style per user. Right now they're siloed."

---

## Link to Your GitHub

> "You can see the full plugin structure at `github.com/xyh/socratic-agent`. It includes the three agent skill definitions, the Ebbinghaus scheduler, and the Obsidian note templates. I'd love to hear your thoughts on the multi-agent architecture — especially whether you think the siloed vs. communicating agent tradeoff is the right call here."

**This last line is key.** It turns the interview from "you talking at them" into "a peer discussion about architecture tradeoffs." That's the signal of a senior/lead candidate.
