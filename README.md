# Study OS — 基于 Claude Code + Obsidian 的引导式学习系统

## 架构

```
Study OS
├── .claude/
│   ├── CLAUDE.md              # 项目级系统提示（苏格拉底式教学 + Obsidian 输出规范）
│   └── commands/
│       ├── learn.md           # /learn 命令：学习材料 → 知识图谱
│       └── quiz.md            # /quiz 命令：交互测验 → 掌握度更新
├── materials/                 # 原始材料（PDF、Markdown、文本）
├── notes/
│   ├── concepts/              # 核心概念笔记（Obsidian 节点）
│   └── sources/               # 源材料索引（Obsidian 节点）
├── quiz/
│   └── retest-queue.md        # 错题重测队列
└── .obsidian/                 # Obsidian 配置（可选）
```

## 快速开始

### 1. 安装依赖

- [Claude Code](https://github.com/anthropics/claude-code) CLI
- [Obsidian](https://obsidian.md/)（桌面端）

### 2. 初始化仓库

```bash
cd ~/code/study-os
git init
git add .
git commit -m "init: Study OS skeleton"
```

### 3. 用 Obsidian 打开

在 Obsidian 中 `打开本地文件夹`，选择 `~/code/study-os`。开启核心插件：
- **Graph View**（知识图谱可视化）
- **Templates**（可选）

### 4. 用 Claude Code 打开

```bash
cd ~/code/study-os
claude
```

## 工作流

### 学习新材料

```
/learn materials/attention-is-all-you-need.pdf
```

Claude Code 会：
1. 拆解材料 → 结构化摘要
2. 苏格拉底式提问带你逐步理解
3. 自动生成 `notes/concepts/*.md` 笔记（含 YAML + 双链）
4. 生成源索引 `notes/sources/attention-is-all-you-need.md`
5. 标记 `quiz_ready: true`

### 查看知识图谱

切换到 Obsidian，按 `Ctrl/Cmd + G` 打开 Graph View。节点 = 概念笔记，边 = `[[双链]]`。

### 测验巩固

```
/quiz
```

Claude Code 会：
1. 从 `quiz_ready: true` 且 `mastery: false` 的笔记中抽题
2. 生成 4 选 1 单选题，交互式提问
3. 答对 → `mastery: true`；答错 → 加入 `quiz/retest-queue.md`
4. 输出薄弱环节报告

### 重测错题

```
/quiz review
```

## 笔记规范

### 概念笔记（`notes/concepts/*.md`）

文件名：英文 slug（如 `self-attention.md`）

```markdown
---
id: self-attention
aliases: [自注意力, SA]
tags: [area/nlp, status/learning, source/attention-is-all-you-need]
created: 2026-05-25
mastery: false
quiz_ready: true
quiz_questions:
  - "Scaled Dot-Product Attention 中除以 sqrt(d_k) 的主要目的是？"
  - "Multi-Head Attention 的 head 数通常是？"
---

# 自注意力机制

## 一句话定义
一种让序列中每个位置都能直接关注其他所有位置的机制。

## 与我已知知识的关联
- [[recurrent-neural-network|RNN]] — 取代了串行隐藏状态传递
- [[softmax|Softmax]] — 在注意力权重归一化中使用

## 细节展开
...
```

### 源索引（`notes/sources/*.md`）

```markdown
---
id: attention-is-all-you-need
type: paper
created: 2026-05-25
---

# 《Attention Is All You Need》

## 核心论点
1. 完全用 Attention 替代 RNN/CNN，实现并行化
2. 提出 Multi-Head Self-Attention + Positional Encoding
3. Transformer 架构成为后续所有大模型的基础

## 涉及的概念
- [[self-attention|自注意力]]
- [[multi-head-attention|多头注意力]]
- [[positional-encoding|位置编码]]
- [[transformer|Transformer]]

## 我的批注
- 2026-05-25: Scaled Dot-Product 的 sqrt(d_k) 是为了防止点积过大导致 softmax 梯度消失
```

## 进阶用法

### 批量学习

```bash
# 在 Claude Code 中
for f in materials/*.pdf; do /learn "$f"; done
```

### 按领域过滤测验

```
/quiz area/nlp
/quiz area/diffusion
/quiz area/zk
```

### 知识图谱缺口分析

```
/analyze-graph
```

（自定义命令：扫描所有概念笔记，找出缺少入链接或出链接的孤立节点）

## 与 30 天冲刺的结合

将 30 天计划中的阅读材料放入 `materials/`，每天用 `/learn` 学习当天论文，学完后用 `/quiz` 测验。Obsidian Graph View 会直观显示知识积累过程。
