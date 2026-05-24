# /quiz

**间隔重复式交互测验**：Quiz Engine Agent 基于 Obsidian 知识图谱出题，Spaced Scheduler Agent 自动调度复习间隔。

## 用法

/quiz [模式] [可选：标签过滤或概念名]

## 模式

- `/quiz` — 标准模式：优先测 `quiz_ready: true` 且未掌握的题目
- `/quiz review` — **艾宾浩斯复习**：只抽 `mastery: true` 但距上次复习已超过间隔阈值的笔记（1天→3天→7天→15天→30天→60天）
- `/quiz mixed` — **混合模式**：50% 新题 + 50% 复习题（推荐日常使用）
- `/quiz interview` — **面试突击**：只抽 `area/nlp|llm|ml` 标签，50% 选择 + 50% 填空推导
- `/quiz random` — 随机 5 题，不限状态
- `/quiz source <slug>` — 只测特定源材料关联的概念
- `/quiz area/<tag>` — 按领域过滤

## 题型

| 题型 | 场景 | 示例 |
|------|------|------|
| **4选1单选** | 概念辨析、常见误区 | "为什么除以 sqrt(d_k) 而不是 d_k？" |
| **填空题** | 公式、参数、关键数字 | "Scaled Dot-Product 除以 ___" |
| **关联题** | 概念间关系 | "RoPE 与绝对位置编码的根本区别是？" |

## 间隔算法

```
intervals = [1, 3, 7, 15, 30, 60]  # 天

答对：interval_index = min(current + 1, 5)
答错：interval_index = 0（重置为 1 天）

下次复习日期 = last_review + intervals[index]
```

## 即时反馈规则

- ✅ 正确：确认 + 深层解释一句话 + 记忆口诀 + `[[concept|查看笔记]]`
- ❌ 错误：正确答案 + 为什么对 + 为什么你选的错 + 记忆口诀 + `[[concept|回看笔记]]`

## 笔记 Frontmatter 更新

```yaml
mastery: true/false
last_review: YYYY-MM-DD
interval_index: N
wrong_count: N
wrong_answers: ["用户错误选项"]
```
