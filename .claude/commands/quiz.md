# /quiz

基于 Obsidian 知识图谱进行**间隔重复式**交互测验，支持选择题与填空题，更新掌握度与记忆曲线。

## 用法

/quiz [模式] [可选：标签过滤或概念名]

## 模式

- `/quiz` — 标准模式：优先测 `quiz_ready: true` 且未掌握的题目
- `/quiz review` — **艾宾浩斯复习**：只抽 `mastery: true` 但距上次复习已超过间隔阈值的笔记（1天→3天→7天→15天→30天）
- `/quiz random` — 随机抽 5 题，不限状态
- `/quiz source <slug>` — 只测特定源材料关联的概念
- `/quiz area/<tag>` — 按领域过滤（如 `/quiz area/nlp`）
- `/quiz mixed` — **混合模式**：50% 新题 + 50% 复习题（推荐日常使用）

## 执行流程

### Phase 1: Pool Building（题库构建）

扫描 `notes/concepts/` 和 `quiz/retest-queue.md`：

**标准模式**：
- 优先 `quiz_ready: true` 且 `mastery: false`
- 其次 `retest-queue.md` 中 `pending` 状态且到期的错题

**艾宾浩斯复习模式 (`/quiz review`)**：
- 读取每个笔记 frontmatter 的 `last_review: YYYY-MM-DD` 和 `interval: N`
- 提取 `interval` 天后到期的已掌握笔记：1天 → 3天 → 7天 → 15天 → 30天
- 若到期，加入复习池；若答对，`interval` 升级到下一档；若答错，重置为 1 天

**混合模式 (`/quiz mixed`)**：
- 50% 新题（未掌握或 quiz_ready）
- 50% 到期复习题（艾宾浩斯队列）

### Phase 2: Question Generation（出题引擎）

每题从笔记内容生成，题型由系统根据概念性质自动选择或随机分配：

#### 选择题（单选 / 4选1）
- 题干明确，选项长度大致相等
- 干扰项基于该领域**常见误解**
- 难度标注：基础 / 进阶 / 推导

#### 填空题（ cloze / 补全 ）
- 抽出一个公式、定义或关键数字，留空
- 要求用户精确填写（如 "除以 ___"、"等于 ___"）
- 适合数学推导、参数设置、算法步骤

#### 关联题（进阶）
- 给出两个概念，问其关系（如 "RoPE 与绝对位置编码的根本区别是？"）
- 选项为 4 种关系描述

### Phase 3: Interactive Quiz（交互测验）

逐题提问，流程：

1. **出题**：展示题干 + 选项（或填空提示）
2. **用户回答**：回复选项字母 / 填空内容
3. **即时反馈**：
   - ✅ 正确：简要确认 + 一句话深层解释 + `[[concept|查看笔记]]` 链接
   - ❌ 错误：正确答案 + 为什么对 + **为什么你选的错** + 关联笔记链接 + 一句话记忆口诀
4. **更新笔记**：
   - 答对：`mastery: true`，`last_review: today`，`interval: 升级到下一档（1→3→7→15→30→60）`
   - 答错：`mastery: false`，`interval: 1`，`wrong_count: N+1`，追加 `wrong_answers: [用户答案]`

### Phase 4: Spaced Repetition Report（间隔重复报告）

测验结束后输出：

```
📊 测验报告
━━━━━━━━━━━━━━━━━━━━
总题数：8  |  新题：4  |  复习：4  |  正确：6  |  错误：2

🧠 记忆曲线更新：
  [[self-attention]]      interval: 1 → 3 天（下次：2026-05-28）
  [[scaled-dot-product]]  interval: 3 → 7 天（下次：2026-06-01）
  [[rope]]                interval: 7 → 15 天（下次：2026-06-10）

❌ 薄弱环节（重置间隔）：
  [[gradient-clipping]]   interval: 15 → 1 天（答错 1 次）
     错误：忘记除以 sqrt(d_k) 的原因
     口诀："点积大了梯度炸，除以根号稳 softmax"

📅 未来复习计划：
  明天：2 个概念
  3天后：3 个概念
  7天后：1 个概念

🔄 建议：
  - 用 /learn 重新过一遍 [[gradient-clipping]]
  - 明天 13:00 提醒自动包含这些复习题
```

### Phase 5: Retest Queue（错题队列）

答错的题自动写入 `quiz/retest-queue.md`，但**优先由艾宾浩斯系统自动调度**，无需手动管理。

## 艾宾浩斯间隔算法

```
intervals = [1, 3, 7, 15, 30, 60]  # 天

答对：index = min(current_index + 1, 5)
答错：index = 0（重置为 1 天）

复习日期 = last_review + intervals[index]
```

## 笔记 Frontmatter 字段要求

每个 `notes/concepts/*.md` 必须包含：

```yaml
---
id: concept-slug
aliases: [中文别名]
tags: [area/xxx, status/learning|mastered, source/xxx]
created: YYYY-MM-DD
mastery: false
quiz_ready: false
quiz_questions:          # 预生成候选题目列表
  - type: choice
    question: "..."
    options: ["A", "B", "C", "D"]
    answer: "B"
    explanation: "..."
  - type: fill
    question: "Scaled Dot-Product 除以 ___"
    answer: "sqrt(d_k)"
    explanation: "..."
last_review:             # 上次复习日期
interval_index: 0        # 当前在 intervals 数组中的位置（0-based）
wrong_count: 0
wrong_answers: []
---
```

## 面试突击模式（临时）

用户可以说 `/quiz interview`：
- 只抽 `tags` 含 `area/nlp` 或 `area/llm` 的概念
- 题型 50% 选择题 + 50% 推导填空
- 每题限时模式（可选），模拟面试压力
- 结束输出：掌握度百分比 + 建议重点复习的 3 个概念
