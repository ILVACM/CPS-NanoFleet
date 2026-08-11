# 接口与数据契约定义

> 本文档索引 NanoFleet 的所有通信契约。核心规范详见 [`AGENTS.md`](../AGENTS.md) §7 数据契约规范。
>
> **铁律**：所有契约必须先定义 Schema 再编写实现，禁止硬编码无类型 `dict`。

---

## 1. WebSocket 消息信封

所有控制面 WebSocket 消息使用统一信封格式，定义详见 [`AGENTS.md`](../AGENTS.md) §7.1。

<!-- TODO: 待 schemas/message.py 落地后补充 WSEnvelope 完整字段说明 -->

---

## 2. 消息类型枚举

`MessageType` 枚举定义详见 [`AGENTS.md`](../AGENTS.md) §7.2。当前覆盖：

- Agent → Server：`REGISTER`、`HEARTBEAT`、`TUNNEL_READY`
- Server → Agent：`REGISTER_ACK`、`TUNNEL_CREATE`、`TUNNEL_CLOSE`、`CONFIG_UPDATE`
- 双向：`PING`、`PONG`、`ERROR`

<!-- TODO: 待新增消息类型时在此同步登记 -->

---

## 3. 各消息类型 Payload 定义

> 每个消息类型的 `payload` 必须有对应的 Pydantic 模型，放在 `schemas/` 目录。

| 消息类型 | Payload 模型 | 定义位置 | 状态 |
| :--- | :--- | :--- | :---: |
| `REGISTER` | `RegisterPayload` | `schemas/node.py` | ⬜ |
| `REGISTER_ACK` | `RegisterAckPayload` | `schemas/node.py` | ⬜ |
| `HEARTBEAT` | `HeartbeatPayload` | `schemas/heartbeat.py` | ⬜ |
| `TUNNEL_READY` | `TunnelReadyPayload` | `schemas/tunnel.py` | ⬜ |
| `TUNNEL_CREATE` | `TunnelCreatePayload` | `schemas/tunnel.py` | ⬜ |
| `TUNNEL_CLOSE` | `TunnelClosePayload` | `schemas/tunnel.py` | ⬜ |
| `CONFIG_UPDATE` | `ConfigUpdatePayload` | `schemas/node.py` | ⬜ |
| `PING` / `PONG` | `PingPongPayload` | `schemas/message.py` | ⬜ |
| `ERROR` | `ErrorPayload` | `schemas/message.py` | ⬜ |

<!-- TODO: 待各 Payload 模型实现后补充字段定义 -->

---

## 4. REST API 端点

<!-- TODO: 待 REST API 设计完成后补充端点清单：
- 节点列表 / 详情
- 隧道管理
- 配置下发
- 认证 -->

---

## 5. 未来契约演进

Phase 2/3 Go 重构时将用 Protobuf 重新定义契约，Python 端的 Pydantic 模型字段语义保持对齐。

<!-- TODO: 待 Go 重构启动后补充 Protobuf schema 映射说明 -->