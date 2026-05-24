# SocraticAgent

> **Learn by derivation, not by digestion.**  
> A multi-agent Socratic learning system for Claude Code — guided derivation, spaced-repetition quiz, and Obsidian knowledge graph.

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-blue)](https://github.com/anthropics/claude-code)
[![Obsidian](https://img.shields.io/badge/Obsidian-Graph-purple)](https://obsidian.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## The Problem

Reading papers sucks. You download a PDF, open it, read the Introduction, hit a wall of math at Section 3, and give up. Three days later you remember almost nothing.

**SocraticAgent** replaces passive reading with **active derivation**.

Instead of summarizing, three specialized AI Agents work together to:
1. **Tutor Agent** — guides you step-by-step through the logic chain
2. **Quiz Agent** — validates understanding at each step
3. **Scheduler Agent** — persists knowledge into an Obsidian graph and schedules spaced-repetition reviews

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    SocraticAgent                         │
├─────────────┬──────────────┬────────────────────────────┤
│  /learn     │   /quiz       │   Obsidian Graph           │
│             │               │                            │
│  Tutor      │   Quiz        │   Scheduler                │
│  Agent      │   Agent       │   Agent                    │
│  ├ Preview  │  ├ Pool build  │  ├ Note generation         │
│  ├ Step 1   │  ├ Ask       │  ├ Quiz seed               │
│  ├ Step 2   │  ├ Feedback   │  ├ Ebbinghaus schedule     │
│  ├ ...      │  ├ Report     │  └ Retest queue            │
│  └ Synthesis│  └ Review     │                            │
└─────────────┴──────────────┴────────────────────────────┘
```

### Agent 1: Socratic Tutor
- Reads your paper/blog/book
- Extracts the core problem + key formula chain
- **Asks before telling**: "What is the variance of Q·K if each element is N(0,1)?"
- Never dumps a 3-paragraph explanation. One step, one question.

### Agent 2: Quiz Engine
- Generates questions targeting **common misconceptions**, not trivia
- 4-choice single-select + fill-in-the-blank + association questions
- Instant feedback with a **memory hook** (rhyme / acronym)
- Wrong answers get a personalized explanation of *why your specific choice was wrong*

### Agent 3: Spaced Scheduler
- Generates structured Obsidian notes with YAML frontmatter
- Pre-seeds 3 quiz questions per concept
- Tracks `mastery`, `interval_index`, `last_review` per note
- Auto-computes Ebbinghaus due dates: **1 → 3 → 7 → 15 → 30 → 60 days**

---

## Quick Start

### 1. Install

```bash
# In any Claude Code project
claude plugins install https://github.com/xyh/socratic-agent
```

Or manually clone into your project:
```bash
cd your-project
git clone https://github.com/xyh/socratic-agent.git .claude/plugins/socratic-agent
```

### 2. Open Obsidian

In Obsidian, `Open folder as vault` → select your project's `notes/` directory.

Press `Ctrl/Cmd + G` to open the **Graph View**. Watch nodes grow as you learn.

### 3. Learn something

```
/learn materials/attention-is-all-you-need.pdf
```

The Tutor Agent will:
1. Show the core problem + formula list
2. Ask if you're ready
3. Guide you through the derivation one step at a time
4. Auto-generate notes in `notes/concepts/` and `notes/sources/`

### 4. Quiz yourself

```
/quiz mixed
```

Pulls 50% new concepts + 50% due-for-review concepts. Answer, get feedback, watch your knowledge graph turn green (`mastery: true`).

### 5. Review tomorrow

```
/quiz review
```

Only concepts whose Ebbinghaus interval has expired. Fight the forgetting curve.

---

## Commands

| Command | Mode | Description |
|---------|------|-------------|
| `/learn <file>` | — | Guided derivation from a paper, blog, or pasted text |
| `/learn --fast <file>` | — | Skip derivation, go straight to summary + interview questions |
| `/quiz` | Standard | New concepts first |
| `/quiz mixed` | **Recommended** | 50% new + 50% review |
| `/quiz review` | Spaced Repetition | Only due-for-review items |
| `/quiz interview` | Interview Prep | NLP/LLM/ML only, 50% choice + 50% derivation fill-in |
| `/quiz source <slug>` | Scoped | Quiz concepts from a specific source |
| `/quiz area/<tag>` | Filtered | e.g. `/quiz area/nlp` |

---

## Interview Mode

Preparing for ML/LLM interviews? Use `/quiz interview`.

- Only `area/nlp`, `area/llm`, `area/ml` concepts
- Derivation fill-in-the-blank questions ("Complete the scaled dot-product formula: ___")
- Time-pressured feel (optional)
- Session ends with a **mastery percentage** and a **weak-link report**

---

## Knowledge Graph

SocraticAgent is designed around **Obsidian** as the knowledge backend.

- Every concept = a node (`notes/concepts/*.md`)
- Every source = a hub linking to its concepts (`notes/sources/*.md`)
- Wikilinks `[[slug|Name]]` create graph edges
- YAML frontmatter tracks learning state

Watch your graph grow from a few isolated nodes to a dense web of interconnected ideas.

---

## Why Not Just Read the Paper?

| | Traditional Reading | SocraticAgent |
|---|---|---|
| **Pace** | You set it (often too fast) | Agent enforces one-step-at-a-time |
| **Retention** | Re-read later (unscheduled) | Ebbinghaus auto-schedules review |
| **Misconceptions** | You don't know what you don't know | Quiz Agent targets common errors |
| **Organization** | Scattered notes | Structured Obsidian graph |
| **Interview prep** | Re-read everything | `/quiz interview` drills weak spots |

---

## Contributing

This project is built for people who learn by building. If you have a better mnemonic for backprop, a cleaner graph layout, or a new question type, PRs welcome.

### Roadmap
- [ ] `/learn` support for arXiv URLs (auto-fetch + parse)
- [ ] `/quiz` support for image-based questions (diagrams, architectures)
- [ ] Plugin marketplace registration (pending Claude Code official approval)
- [ ] Sync with Anki deck export

---

## License

MIT

---

> *"I cannot teach anybody anything. I can only make them think."* — Socrates
