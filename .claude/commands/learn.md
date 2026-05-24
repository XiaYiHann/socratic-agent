# /learn

**引导式推导学习**：不直接给摘要，而是通过苏格拉底式对话带领用户从摘要到细节逐步推导，最终沉淀为 Obsidian 知识图谱笔记。

## 用法

/learn <文件路径、URL 或粘贴的文本>

## 核心理念

> 不要逐行读论文。让 AI 带你**推导**。

传统学习：下载 PDF → 打开 → 读 Introduction → 卡住 → 放弃。  
引导式学习：AI 先读 → 拆解逻辑链条 → **每次只暴露一个推导步骤** → 用户思考 → 确认 → 下一步。

## 执行流程

### Phase 1: Source Ingestion（材料摄入）

读取输入（PDF / URL / 粘贴文本），提取：
- **标题 + 作者 + 年份**
- **核心问题**（这篇论文想解决什么？）
- **核心方法**（一句话概括解法）
- **关键公式列表**（仅列出，不解释）
- **重要图表的文字描述**

**输出给用户**：

```
📄 《Attention Is All You Need》(Vaswani et al., 2017)
━━━━━━━━━━━━━━━━━━━━
核心问题：如何用纯注意力机制替代 RNN/CNN，实现并行化序列建模？
核心方法：Transformer = Multi-Head Self-Attention + Position-wise FFN + Residual/LN
关键公式：
  (1) Attention(Q,K,V) = softmax(QK^T / sqrt(d_k)) V
  (2) MultiHead = Concat(head_1, ..., head_h) W^O
  (3) PE(pos, 2i) = sin(pos / 10000^(2i/d_model))

我们一步一步来推导。准备好了吗？
```

用户回复 `y` 或 `ok` 后，进入 Phase 2。

### Phase 2: Guided Derivation（引导式推导）

**规则**（严格遵守）：

1. **每次只暴露一个推导步骤**：公式(1) 的动机 → Q/K/V 是什么 → 为什么要 softmax → 为什么要除以 sqrt(d_k) → 为什么要 Multi-Head → Positional Encoding 的直觉
2. **给出直觉/动机后，停住提问**：
   - "你觉得如果不除以 sqrt(d_k)，softmax 的输出会有什么变化？"
   - "假设 d_k = 64，Q·K 的均值大约是多少量级？"
3. **用户回答后**：
   - 对 → 确认 + 补充细节，进入下一步
   - 错 → 不直接给答案，用更简单的问题引导发现（如 "如果两个随机向量的点积方差是 d_k，那标准差是多少？"）
   - 卡太久 → 给提示："提示：想想方差和标准差的关系。"
4. **推导完核心链条后**：
   - "现在我们有 Attention 块了。你觉得只靠 Attention 能区分 '我吃苹果' 和 '苹果吃我' 吗？"
   - 引导发现位置编码的必要性

**禁止**：
- 一次性输出超过 2 个公式推导步骤
- 直接说 "答案是 B" 而不解释为什么
- 跳过用户的回答直接进入下一步

### Phase 3: Concept Extraction（概念提取）

推导结束后，AI 回顾对话，识别所有被触及的核心概念：
- 已存在 `notes/concepts/` 中的 → 追加内容 + 建立反向链接
- 新出现的 → 标记为待创建

### Phase 4: Note Generation（笔记生成）

为每个新概念生成 Obsidian 笔记，写入 `notes/concepts/`：

```markdown
---
id: scaled-dot-product
aliases: [缩放点积]
tags: [area/nlp, status/learning, source/attention-is-all-you-need]
created: 2026-05-25
mastery: false
quiz_ready: false
quiz_questions:
  - type: fill
    question: "Scaled Dot-Product 除以 ___"
    answer: "sqrt(d_k)"
    explanation: "防止点积过大导致 softmax 梯度消失"
  - type: choice
    question: "为什么除以 sqrt(d_k) 而不是 d_k？"
    options: [
      "因为 d_k 太大",
      "因为需要保持方差稳定为 1",
      "因为 softmax 喜欢小数",
      "因为作者随便选的"
    ]
    answer: "B"
    explanation: "Q,K 独立随机时，点积方差 ≈ d_k，除以 sqrt(d_k) 后标准差 ≈ 1，softmax 输入稳定"
last_review:
interval_index: 0
wrong_count: 0
wrong_answers: []
---

# Scaled Dot-Product

## 一句话定义
点积注意力中除以 sqrt(d_k) 以稳定 softmax 输入分布。

## 为什么重要
不缩放时，d_k 大的情况下 softmax 梯度趋近于 0，模型无法学习。

## 推导直觉
1. Q, K 独立随机初始化，每个元素 ~ N(0,1)
2. Q·K = Σ(q_i * k_i)，共 d_k 项
3. E[Q·K] = 0, Var(Q·K) = d_k
4. 所以 Q·K ~ N(0, d_k)，标准差 = sqrt(d_k)
5. 除以 sqrt(d_k) 后，标准差 ≈ 1，softmax 输入在 [-2, 2] 之间，梯度健康

## 与我已知知识的关联
- [[self-attention|自注意力]] — 在其内部使用
- [[softmax|Softmax]] — 缩放是为了保护其梯度
- [[gradient-vanishing|梯度消失]] — 不缩放的后果

## 还不确定的问题
- [ ] d_k 很小时（如 8）是否还需要缩放？
```

### Phase 5: Source Index（源材料索引）

生成 `notes/sources/<slug>.md`，记录：
- 核心问题 → 方法 → 结论 的三段式
- 所有涉及的概念双链
- 推导对话中的关键批注

### Phase 6: Quiz Seed（测验种子）

每个新笔记的 `quiz_questions` 预填充：
- **2 道选择题**：1 基础概念 + 1 推导细节
- **1 道填空题**：公式或数字填空
- `quiz_ready: true`

## 快速学习模式（面试冲刺）

用户可以说 `/learn --fast <材料>`：
- 跳过逐步推导，直接给结构化摘要 + 关键公式 + 面试常见问题列表
- 但仍然生成笔记，只是 `quiz_questions` 从常见面试题库里选

## 学习材料类型支持

| 类型 | 处理方式 |
|------|---------|
| arXiv 论文 PDF | 提取结构：Abstract → Intro → Method → Exp |
| 技术博客 URL | 提取标题/作者/核心论点/代码片段 |
| 书籍章节 | 按页码分段，每段作为一次对话单元 |
| 视频/讲座 | 用户粘贴字幕/笔记，AI 结构化 |
| 自己的笔记 | 追问深挖，补全缺失链接 |
