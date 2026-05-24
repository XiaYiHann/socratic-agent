# /learn

学习并消化用户提供的材料，最终沉淀为 Obsidian 知识图谱笔记。

## 用法

/learn <文件路径、URL 或粘贴的文本>

## 执行流程

### Phase 1: Source Analysis（源材料拆解）
读取输入材料，生成结构化摘要：
- 核心论点（不超过 3 条）
- 关键公式 / 算法步骤
- 重要图表的文字描述
- 作者想要解决什么问题

### Phase 2: Guided Exploration（引导式探索）
用苏格拉底式提问带领用户逐步理解材料。规则：
- 每次只引入 **1 个新概念** 或 **1 个推导步骤**
- 给出直觉/动机后，让用户用自己的话复述或回答一个选择题
- 用户确认理解后，才进入下一步
- 如果发现用户卡住，退回上一步换种方式解释

### Phase 3: Concept Extraction（概念提取）
识别材料中的所有核心概念，检查 `notes/concepts/` 中是否已有同名笔记：
- 如果已有：补充新内容到现有笔记，建立反向链接
- 如果没有：创建新笔记文件

### Phase 4: Note Generation（笔记生成）
为每个新概念生成 Obsidian 格式笔记，写入 `notes/concepts/`：
- 文件名：英文 slug（如 `self-attention.md`）
- 必须包含 YAML frontmatter（id, aliases, tags, created, mastery, quiz_ready）
- 必须包含 `[[双链]]` 关联到已知概念和源材料索引
- 可选：画 ASCII 图解释结构

### Phase 5: Source Index（源材料索引）
在 `notes/sources/` 下创建源材料索引文件，链接到所有相关概念笔记。

### Phase 6: Quiz Seed（测验种子）
为每个新笔记生成 2-3 个候选测验题，写入笔记 frontmatter 的 `quiz_questions` 列表，并将 `quiz_ready` 设为 `true`。

## 输出示例

完成时汇报：
```
✅ 学习完成：注意力机制
━━━━━━━━━━━━━━━━━━━━
生成笔记：
  notes/concepts/self-attention.md（新）
  notes/concepts/scaled-dot-product.md（新）
  notes/concepts/multi-head-attention.md（补充）
源索引：
  notes/sources/attention-is-all-you-need.md
待测概念：3 个（quiz_ready = true）
```
