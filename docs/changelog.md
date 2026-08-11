# 项目变更日志

> 本文档记录 NanoFleet 项目的架构决策记录（ADR）与重大变更。
>
> **规则**：本文件只追加不删减，按时间倒序排列（最新在前）。

---

<!-- 变更记录模板：
## YYYY-MM-DD — 变更标题

**变更内容**：
- ...

**变更原因**：
- ...

**影响范围**：
- ...

**关联文档**：
- ...
-->

## 2026-08-10 — 阶段重规划：Phase 2 引入 gRPC 技术验证

**变更内容**：
- Phase 2 定位从"隧道与终端能力补全"调整为"gRPC 技术验证 + 功能补全"
- Phase 2 技术栈明确为 Python + gRPC（仍禁止 Go 代码）
- Phase 3 定位从"Golang 重构"调整为"Golang 生产重构"，从 Phase 2 Python 代码迁移
- 更新 `docs/roadmap.md` 阶段定义和任务拆解
- 更新 `AGENTS.md` §4.2 技术栈表、新增 §4.3 生产阶段、更新 §11 阶段状态

**变更原因**：
- 用户希望在 Phase 2 提前验证 gRPC 通信模式，降低 Phase 3 技术风险
- Python 生态对 gRPC 支持成熟（grpcio + protobuf），可在不引入 Go 的前提下完成技术验证
- Phase 2 积累的 gRPC 经验和 protobuf 定义可直接复用到 Phase 3 Go 重构

**影响范围**：
- `docs/roadmap.md`（阶段定义和任务拆解）
- `AGENTS.md`（§4.2 技术栈、§11 阶段状态）
- `docs/changelog.md`（本记录）

**关联文档**：
- `docs/roadmap.md` §1-§4
- `AGENTS.md` §4、§11

## 2026-08-07 — 文档体系重构：doc → docs + prompts 集成 + 全文档精简

**变更内容**：
- `doc/` 目录重命名为 `docs/`（单数改复数，符合社区惯例）
- 新增 `prompts/` 目录及其子结构（`decisions/`、`features/`、`reviews/`、`ops/`），用于存储 AI 提示词
- 创建 `docs/` 下 8 篇精简骨架文档（architecture、api-contracts、cli-spec、db-schema、roadmap、coding-standards、deployment、changelog）
- 创建 `prompts/README.md` 使用说明
- 更新 `AGENTS.md`：§2 索引路径更新、§2.1 内容说明精简、新增 §2.4 提示词库索引、§6 目录树新增 prompts/、§12.3 流程新增 prompts/ 检查项与自检条目
- 更新 `README.md`：路径引用更新、约束性内容（铁律详表、技术栈理由列）改为引用 AGENTS.md、目录结构新增 prompts/
- 全局将所有 `doc/` 路径引用替换为 `docs/`

**变更原因**：
- `docs/` 符合社区惯例（复数命名）
- `prompts/` 使 AI 协作过程中的关键决策可追溯、操作流程可复用
- 原文档内容过于臃肿，包含大量尚未实现的预设细节，容易误导后续开发；精简为最小可用骨架，细节留白，后续根据实际开发按需补充

**影响范围**：
- `AGENTS.md`（全局约束入口）
- `README.md`（展示窗口）
- `docs/` 目录（全部文档）
- `prompts/` 目录（新增）

**关联文档**：
- `AGENTS.md` §2、§6、§12.3
- `prompts/README.md`