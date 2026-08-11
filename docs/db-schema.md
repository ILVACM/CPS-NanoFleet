# 数据库表结构设计

> NanoFleet 原型期使用 SQLite（零外部依赖）。存储层必须实现 `StorageInterface`，详见 [`AGENTS.md`](../AGENTS.md) §3 铁律 3。

---

## 1. 表清单

| 表名 | 用途 | 状态 |
| :--- | :--- | :---: |
| `nodes` | 节点注册信息（ID、主机名、IP、状态、最后心跳时间） | ⬜ |
| `heartbeat_logs` | 心跳历史记录（CPU、内存、磁盘、网络） | ⬜ |
| `users` | Server Web UI 登录用户 | ⬜ |
| `tunnel_mappings` | 隧道映射关系（节点 ID ↔ 本地监听端口） | ⬜ |

<!-- TODO: 待各表字段实际确定后补充 DDL -->

---

## 2. 字段定义

<!-- TODO: 待表结构落地后按表补充：
- 字段名、类型、约束
- 索引设计
- 外键关系 -->

---

## 3. 迁移策略

<!-- TODO: 待确定迁移工具（如 Alembic）后补充迁移方案 -->

---

## 4. 未来演进

Phase 2/3 可替换为 PostgreSQL，通过 `StorageInterface` 屏蔽底层差异。

<!-- TODO: 待存储层接口实现后补充接口方法清单 -->