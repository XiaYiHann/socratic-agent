# /quiz

基于 Obsidian 知识图谱进行交互式选择题测验，更新掌握度。

## 用法

/quiz [可选：标签过滤或概念名]

## 执行流程

### Phase 1: Pool Building（题库构建）
扫描 `notes/concepts/`，筛选：
- 优先 `quiz_ready: true` 且 `mastery: false` 的笔记
- 如果没有足够题目，随机抽取已掌握笔记作为复习
- 支持按 `tags: [area/xxx]` 过滤（如 `/quiz area/nlp`）

### Phase 2: Question Generation（出题）
基于笔记内容生成 **4 选 1 单选题**。出题规则：
- 选项长度大致相等（避免用户靠长度猜答案）
- 干扰项必须基于该领域的**常见误解**（如把 scaled dot-product 的 sqrt(d_k) 说成 d_k 或 1/d_k）
- 题干明确，不玩文字游戏
- 每题标注难度：基础 / 进阶 / 推导

### Phase 3: Interactive Quiz（交互测验）
逐题提问，流程：
1. 展示题干 + 4 个选项（A/B/C/D）
2. 用户回答选项
3. 立即反馈：
   - 对：简要确认，链接到相关笔记 `[[concept|查看笔记]]`
   - 错：给出正确答案 + 为什么对 + 为什么用户选的选项错 + 关联笔记链接
4. 更新笔记 frontmatter：
   - 答对：`mastery: true`，`last_quiz_date: YYYY-MM-DD`
   - 答错：`mastery: false`，`wrong_count: N+1`，追加 `wrong_answers: [用户错误选项]`

### Phase 4: Weak Link Report（薄弱环节报告）
测验结束后输出：
```
📊 测验报告
━━━━━━━━━━━━━━━━━━━━
总题数：5  |  正确：3  |  错误：2  |  掌握率：60%

❌ 薄弱环节：
  1. [[scaled-dot-product|Scaled Dot-Product]] — 答错 2 次
     问题：忘记了除以 sqrt(d_k) 的原因
  2. [[rope|RoPE]] — 答错 1 次
     问题：混淆了绝对位置编码和相对位置编码

🔗 知识图谱缺口：
  Scaled Dot-Product 缺少与 [[gradient-vanishing|梯度消失]] 的链接

🔄 建议：
  - 用 /learn 重新学习 [[scaled-dot-product]]
  - 3 天后自动重测（已加入 quiz/retest-queue.md）
```

### Phase 5: Retest Queue（重测队列）
将错题加入 `quiz/retest-queue.md`：
```markdown
---
generated: YYYY-MM-DD
---

# 重测队列

| 概念 | 错题数 | 建议重测日期 | 状态 |
|------|--------|-------------|------|
| [[scaled-dot-product]] | 2 | YYYY-MM-DD + 3 | pending |
```

## 特殊模式

- `/quiz random` — 随机抽 5 题，不限状态
- `/quiz review` — 只测已掌握笔记，防止遗忘
- `/quiz source <source-slug>` — 只测来自特定源材料的概念
