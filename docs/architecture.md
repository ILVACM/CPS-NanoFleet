# 系统架构设计

> 本文档描述 NanoFleet 的系统架构。核心约束详见 [`AGENTS.md`](../AGENTS.md) §3 架构铁律。

---

## 1. 架构模式

NanoFleet 采用 **C/S 模式**（Client/Server）：

| 角色 | 部署位置 | 职责 |
| :--- | :--- | :--- |
| **Server** | 管理节点（一台） | 节点注册中心、隧道网关、Web Dashboard、存储 |
| **Agent** | 被管节点（多台） | 心跳上报、反向隧道拨号、命令执行 |

<!-- TODO: 待 Server/Agent 实际部署形态确定后补充网络拓扑图 -->

---

## 2. 控制面与数据面分离

详见 [`AGENTS.md`](../AGENTS.md) §3 铁律 1。此处仅记录架构层面的流向划分：

- **控制面**：Agent ↔ Server 之间的信令通信（注册、心跳、隧道信令、配置下发）
- **数据面**：Agent ↔ Server 之间的字节流传输（SSH 终端、端口转发、文件传输）

<!-- TODO: 待通信流程实际实现后补充时序图 -->

---

## 3. 模块划分

| 端 | 模块 | 职责 | 对应接口 |
| :--- | :--- | :--- | :--- |
| Server | `registry/` | 节点注册与生命周期管理 | — |
| Server | `gateway/` | 隧道网关（数据面入口） | `TunnelInterface` |
| Server | `storage/` | 状态持久化 | `StorageInterface` |
| Server | `api/` | REST + WebSocket 路由 | — |
| Server | `discovery/` | mDNS 广播（让 Agent 发现 Server） | `DiscoveryInterface` |
| Agent | `discovery/` | 网络嗅探（发现 Server） | `DiscoveryInterface` |
| Agent | `heartbeat/` | 心跳采集与上报 | — |
| Agent | `tunnel/` | 反向隧道拨号器（数据面） | `TunnelInterface` |

> 接口定义要求详见 [`AGENTS.md`](../AGENTS.md) §3 铁律 3。

<!-- TODO: 待各模块接口定义落地后补充接口签名清单 -->

---

## 4. 通信流程

<!-- TODO: 待实际实现后补充以下流程：
- Agent 注册流程
- 心跳上报流程
- 隧道建立流程
- SSH 登录流程
- 配置下发流程 -->

---

## 5. 未来演进

Phase 2/3 计划用 Golang 重构，架构契约保持不变。详见 [`roadmap.md`](./roadmap.md)。

<!-- TODO: 待 Go 重构启动后补充语言迁移架构影响说明 -->