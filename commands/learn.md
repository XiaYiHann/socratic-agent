# /learn

**引导式推导学习**：Socratic Tutor Agent 通过苏格拉底式对话带领用户从摘要到细节逐步推导，最终由 Scheduler Agent 沉淀为 Obsidian 知识图谱笔记。

## 用法

/learn <文件路径、URL 或粘贴的文本>

## 核心理念

> 不要逐行读论文。让 AI **Agent** 带你推导。

传统学习：下载 PDF → 硬读 Introduction → 卡住 → 放弃。
**SocraticAgent**：Tutor Agent 先读 → 拆解逻辑链条 → 每次只暴露一个推导步骤 → 用户思考 → Quiz Agent 验证 → Scheduler Agent 排期复习。

## 执行流程（多 Agent 协作）

### Agent 1: Socratic Tutor
- 读取输入材料，提取核心问题 + 关键公式列表
- **每次只暴露 1 个推导步骤**，给出直觉后停住提问
- 用户回答 → 对则进下一步，错则给提示不直接给答案
- 推导完核心链条后，引导发现延伸概念（如位置编码的必要性）

### Agent 2: Quiz Engine（即时验证）
- 每完成一个概念推导后，生成 1 道即时验证题
- 用户答对 → Tutor 继续；答错 → Tutor 退回上一步换方式解释

### Agent 3: Spaced Scheduler（笔记沉淀）
- 推导结束后，自动提取所有核心概念
- 生成 Obsidian 笔记（`notes/concepts/*.md`）+ 源索引（`notes/sources/*.md`）
- 预生成 3 道候选测验题，标记 `quiz_ready: true`，`interval_index: 0`

## 输出示例

```
📄 《Attention Is All You Need》(Vaswani et al., 2017)
━━━━━━━━━━━━━━━━━━━━
核心问题：如何用纯注意力机制替代 RNN/CNN，实现并行化序列建模？
关键公式：
  (1) Attention(Q,K,V) = softmax(QK^T / sqrt(d_k)) V
  (2) MultiHead = Concat(head_1, ..., head_h) W^O
  (3) PE(pos, 2i) = sin(pos / 10000^(2i/d_model))

我们一步一步来推导。准备好了吗？
[用户: y]

Step 1/5: 为什么除以 sqrt(d_k)？
→ Tutor: "假设 Q,K 独立随机，每个元素 ~ N(0,1)。Q·K 的方差大约是多少？"
[用户: d_k]
→ Tutor: "对。标准差 = sqrt(d_k)。如果不除，softmax 的输入可能多大？"
...

✅ 推导完成。Scheduler 已生成 3 个概念笔记：
  notes/concepts/self-attention.md
  notes/concepts/scaled-dot-product.md
  notes/concepts/positional-encoding.md
```
