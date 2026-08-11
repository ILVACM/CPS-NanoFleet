# 开发路线图

> 本文档记录 NanoFleet 的开发里程碑与任务状态。当前任务优先级队列详见 [`AGENTS.md`](../AGENTS.md) §11。

---

## 1. 阶段划分

| 阶段 | 定位 | 状态 |
| :--- | :--- | :---: |
| **Phase 1** | Python 原型验证 | 🟡 进行中 |
| **Phase 2** | gRPC 技术验证 + 功能补全 | ⬜ 待开始 |
| **Phase 3** | Golang 生产重构 | ⬜ 待开始 |

---

## 2. Phase 1 — Python 原型验证

**里程碑目标**：在 Web 上看到局域网内 3 台虚拟机的实时状态。

任务优先级队列详见 [`AGENTS.md`](../AGENTS.md) §11。

<!-- TODO: 待各任务启动后补充：
- 具体验收标准
- 依赖关系
- 当前阻塞项 -->

---

## 3. Phase 2 — gRPC 技术验证 + 功能补全

<!-- TODO: 待 Phase 1 接近完成后补充 Phase 2 任务拆解：
- gRPC 控制面原型（替换 WebSocket）
- Python gRPC Server/Agent 实现
- 反向隧道实现
- xterm.js Web SSH
- CLI 工具完善
- 端口转发 -->

---

## 4. Phase 3 — Golang 生产重构

<!-- TODO: 待 Phase 2 完成后补充 Phase 3 任务拆解：
- Go Server/Agent 重写（从 Phase 2 Python 代码迁移）
- gRPC 控制面（从 Phase 2 gRPC 原型迁移）
- 高性能 TCP 转发
- v1.0.0 发布 -->

> ⚠️ Phase 1 禁止编写 Go 代码；Phase 2 允许 gRPC + Python，禁止 Go；Phase 3 开始 Go 重构。详见 [`AGENTS.md`](../AGENTS.md) §10 禁止事项 #4。