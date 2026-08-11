# 知识库索引

> **适用范围**：AI Agent（用于快速定位目标文档，不替代文档内容本身）
>
> **最后更新**：2026-08-10

---

## 文档清单

| 文件路径 | 标题 | 摘要 | 标签/分类 | 最后更新 |
| :--- | :--- | :--- | :--- | :--- |
| `architecture.md` | 系统架构设计 | 描述 NanoFleet C/S 架构模式、控制面与数据面分离、模块划分及通信流程，当前为骨架文档，通信流程与接口签名待补充 | 架构 | 2026-08-07 |
| `api-contracts.md` | 接口与数据契约定义 | 索引所有通信契约：WebSocket 消息信封、消息类型枚举、Payload 模型定义及 REST API 端点，Payload 字段与 REST 端点均为 TODO | 接口 | 2026-08-07 |
| `cli-spec.md` | CLI 命令规范 | 规范 `nf` CLI 命令树、全局 Flag 及输出格式，子命令详细参数与输出示例待各命令实现后补充 | 规范 | 2026-08-07 |
| `db-schema.md` | 数据库表结构设计 | 原型期 SQLite 表清单（nodes、heartbeat_logs、users、tunnel_mappings），字段定义、索引设计与迁移策略均为 TODO | 架构/数据 | 2026-08-07 |
| `roadmap.md` | 开发路线图 | 记录三阶段开发计划：Phase 1 Python 原型验证（进行中）、Phase 2 隧道与终端、Phase 3 Golang 重构，Phase 2/3 任务拆解待补充 | 项目管理 | 2026-08-07 |
| `coding-standards.md` | 编码规范细则 | 补充 AGENTS.md §5 未展开的编码细则，涵盖 Python、Vue/TypeScript、测试规范及代码审查清单，均为 TODO | 规范 | 2026-08-07 |
| `deployment.md` | 部署方案 | 记录四种部署方式（单文件打包、systemd、U 盘一键、安装脚本）及配置管理，各方式详细步骤待实现后补充 | 运维 | 2026-08-07 |
| `changelog.md` | 项目变更日志 | 记录架构决策记录（ADR）与重大变更，按时间倒序排列，仅追加不删减；当前含 2026-08-07 文档体系重构记录 | 项目管理 | 2026-08-07 |

---

## 路由说明

Agent 可按关键词或功能域快速定位目标文档：

### 按功能域分类

| 功能域 | 关键词 | 目标文档 |
| :--- | :--- | :--- |
| **架构** | 架构模式、C/S、模块划分、控制面、数据面、通信流程 | [`architecture.md`](./architecture.md) |
| **接口** | WebSocket、消息信封、Payload、REST API、数据契约、Schema | [`api-contracts.md`](./api-contracts.md) |
| **数据** | 数据库、SQLite、表结构、DDL、迁移、StorageInterface | [`db-schema.md`](./db-schema.md) |
| **规范** | CLI、命令、nf、编码规范、Python 规范、Vue 规范、测试、代码审查 | [`cli-spec.md`](./cli-spec.md)、[`coding-standards.md`](./coding-standards.md) |
| **运维** | 部署、打包、systemd、安装脚本、U 盘部署、配置管理 | [`deployment.md`](./deployment.md) |
| **项目管理** | 路线图、里程碑、Phase、变更日志、ADR | [`roadmap.md`](./roadmap.md)、[`changelog.md`](./changelog.md) |

### 路由逻辑

1. **关键词命中单一文档** → 直接定位该文档
2. **关键词命中多个文档** → 按功能域优先级选取：架构 > 接口 > 数据 > 规范 > 运维 > 项目管理
3. **关键词未命中** → 回退到 [`architecture.md`](./architecture.md) 作为入口文档，或查阅 [`AGENTS.md`](../AGENTS.md) 获取全局约束

---

## 维护规则

1. **新增文档**：在 `docs/` 下新增 `.md` 文件时，必须同步在本文档清单表中添加对应条目，并在路由说明中补充关键词映射
2. **删除文档**：删除 `docs/` 下文件时，必须同步移除本文档清单表中对应条目及路由说明中相关映射
3. **修改文档**：文档标题、摘要、分类发生变更时，必须同步更新本文档清单表中对应条目
4. **一一对应**：清单表中的文件路径必须与 `docs/` 目录下实际文件一一对应，无遗漏、无多余
5. **更新时间**：每次修改文档内容时，同步更新清单表中该条目的「最后更新」日期为实际修改日期