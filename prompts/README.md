# AI 提示词库

> 存储 NanoFleet 项目开发过程中重要关键节点的 AI 提示词原文与上下文，使设计决策可追溯、操作流程可复用。

---

## 目录结构

| 目录 | 内容 |
| :--- | :--- |
| `decisions/` | 架构选型、技术栈变更、铁律制定等决策推导过程 |
| `features/` | 功能规格定义、实现引导、契约设计 |
| `reviews/` | 代码审查、文档一致性校验、规范合规检查 |
| `ops/` | 文档体系搭建、CI/CD、工具链配置等基础设施操作 |

---

## 文件格式

- **命名**：`NNN-kebab-case-slug.md`（如 `001-control-data-plane-separation.md`）
- **结构**：每个文件必须包含三段：`Context`（背景）、`Prompt`（提示词正文）、`Outcome`（结果摘要）
- **关联**：若执行后导致文档/代码变更，必须在 `Outcome` 中记录关联变更
- **废弃**：被替代的提示词不删除，标注状态为"已废弃"并说明替代方案

### 最小示例

```markdown
# 001-control-data-plane-separation

## Context

控制面与数据面是否应共用 WebSocket 通道？

## Prompt

分析将 SSH 字节流塞入 WebSocket Binary Frame 的利弊，
对比双通道方案，给出推荐。

## Outcome

- 决策：采用双通道分离（控制面 WebSocket + 数据面 Raw TCP）
- 关联变更：AGENTS.md §3 铁律 1、docs/architecture.md §2
```

---

## 与知识库的关系

本目录是 [`AGENTS.md`](../AGENTS.md) §2.4 索引的提示词库。提示词记录"为什么这样设计"的推导过程，而 `AGENTS.md` + `docs/` 记录"最终设计了什么"。两者互补：`prompts/` 是过程存档，`docs/` 是结果存档。