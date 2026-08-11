# AGENTS.md — NanoFleet 项目 AI Agent 全局约束与知识库入口

> **本文件是 NanoFleet 项目面向所有 AI Agent / LLM 辅助开发工具的唯一权威约束文件。**
>
> 无论你是 Trae、MimoCode、Cursor、Continue、Copilot、Windsurf 还是任何其他 AI 编码助手，
> 在为本项目生成、修改、审查任何代码或文档之前，**必须完整阅读并遵守本文件中的所有规则**。

---

## 📑 目录

1. [项目身份速览](#1-项目身份速览)
2. [文档知识库索引](#2-文档知识库索引)
3. [架构铁律（不可违反）](#3-架构铁律不可违反)
4. [技术栈规范](#4-技术栈规范)
5. [编码规范](#5-编码规范)
6. [目录结构规范](#6-目录结构规范)
7. [数据契约规范](#7-数据契约规范)
8. [CLI 规范](#8-cli-规范)
9. [错误处理与日志规范](#9-错误处理与日志规范)
10. [禁止事项清单](#10-禁止事项清单)
11. [当前开发阶段与任务状态](#11-当前开发阶段与任务状态)
12. [与 Agent 协作的工作流](#12-与-agent-协作的工作流)
13. [术语表](#13-术语表)

---

## 1. 项目身份速览

| 属性 | 值 |
| :--- | :--- |
| **项目名** | NanoFleet |
| **CLI 命令** | `nf` |
| **Slogan** | *A nano-sized fleet commander for your lab servers.* |
| **定位** | 面向高校实验室 / 中小企业机房 / HomeLab 的轻量级局域网服务器集群管理与 SSH 堡垒机系统 |
| **核心场景** | 校园网（无固定 IP、DHCP、端口限制、DPI）环境下管理 8~50 台 Linux 服务器 |
| **核心能力** | 节点自动发现（mDNS/UDP）、状态监控、反向隧道 SSH、Web Dashboard、一键部署 |
| **对标产品** | Teleport（精神导师）、JumpServer（反面教材——太重）、frp（仅穿透不够） |
| **当前阶段** | Phase 1 — Python 原型验证期 |
| **作者** | 广东工业大学 软件工程专业本科生 |
| **许可证** | MIT（规划中） |

---

## 2. 文档知识库索引

> 本项目的详细设计文档统一存放在 `./docs/` 目录下，AI 提示词库存放在 `./prompts/` 目录下。
> 当你在执行具体任务时，**必须**根据任务类型查阅对应的文档，而非凭空臆造设计。

### 2.1 文档目录总览

| 文件路径 | 内容说明 | 何时查阅 |
| :--- | :--- | :--- |
| [`docs/architecture.md`](./docs/architecture.md) | 系统架构设计 | 涉及模块设计、通信流程、架构决策时 |
| [`docs/api-contracts.md`](./docs/api-contracts.md) | 接口与数据契约定义 | 编写任何网络通信代码、数据结构时 |
| [`docs/cli-spec.md`](./docs/cli-spec.md) | `nf` CLI 命令规范 | 编写 CLI 相关代码时 |
| [`docs/db-schema.md`](./docs/db-schema.md) | SQLite 数据库表结构设计 | 编写存储层、数据库操作代码时 |
| [`docs/roadmap.md`](./docs/roadmap.md) | 开发路线图与里程碑 | 确认当前任务优先级、规划下一步时 |
| [`docs/coding-standards.md`](./docs/coding-standards.md) | 编码规范细则 | 编写或审查任何代码时 |
| [`docs/deployment.md`](./docs/deployment.md) | 部署方案 | 涉及部署、打包、安装脚本时 |
| [`docs/changelog.md`](./docs/changelog.md) | 项目变更日志 | 需要了解"为什么这样设计"时 |
| [`docs/INDEX.md`](./docs/INDEX.md) | 知识库索引与路由 | 首次查阅知识库时（作为路由入口） |

### 2.2 索引规则

- **知识库路由路径**：Agent 查阅流程为 `AGENTS.md → docs/INDEX.md → 目标文档`。`docs/INDEX.md` 是知识库的智能路由入口，提供按关键词/功能域快速定位目标文档的能力。禁止跳过 INDEX.md 直接猜测文档路径。
- **优先级**：本文件 (`AGENTS.md`) > `docs/` 下的具体文档 > 代码中的注释。
- **冲突处理**：若 `docs/` 中的某篇文档与本文件冲突，以本文件为准（本文件是全局约束）。
- **文档缺失**：若某篇 `docs/` 文档尚未创建，则依据本文件中的规则进行推断，并在生成内容时标注 `<!-- TODO: 待补充至 docs/xxx.md -->`。
- **文档更新**：当你的代码实现导致设计发生变更时，**必须**同步更新对应的 `docs/` 文档，并在 `docs/changelog.md` 中记录变更。

### 2.3 文档职责边界（重要）

| 文件 | 职责定位 | 面向对象 | 承担约束？ |
| :--- | :--- | :--- | :---: |
| `README.md` | 项目展示窗口：向人类介绍项目要点、引导快速理解与使用 | 人类访客 / 潜在用户 | ❌ |
| `AGENTS.md` | 全局约束入口：开发规范、架构铁律、技术栈限定、工作流规则 | AI Agent / 开发者 | ✅ |
| `docs/*.md` | 详细设计文档：具体模块的深入设计与实现细节 | AI Agent / 开发者 | ✅ |

> **核心原则**：`README.md` **不承担**开发规范的约束规定或要求说明功能。它的主要功能是向人类展示知识库的要点，并引导人类快速理解和使用本项目。所有约束性内容归 `AGENTS.md` 和 `docs/` 管辖。

### 2.4 提示词库索引

> `prompts/` 目录存储项目开发过程中重要关键节点的 AI 提示词原文与上下文。
> 当需要理解某个设计决策的由来、复用某个操作流程、或执行标准化审查时，查阅对应提示词。

| 目录 | 内容 | 何时查阅 |
| :--- | :--- | :--- |
| `prompts/decisions/` | 架构选型、技术栈变更、铁律制定等决策推导过程 | 需要理解"为什么这样设计"时 |
| `prompts/features/` | 功能规格定义、实现引导、契约设计 | 开发类似功能、复用开发流程时 |
| `prompts/reviews/` | 代码审查、文档一致性校验、规范合规检查 | 执行审查任务时 |
| `prompts/ops/` | 文档体系搭建、CI/CD、工具链配置等基础设施操作 | 配置项目基础设施时 |

**规则**：
- 提示词文件命名格式：`NNN-kebab-case-slug.md`（如 `001-control-data-plane-separation.md`）
- 每个提示词文件必须包含：Context（背景）、Prompt（提示词正文）、Outcome（结果摘要）
- 提示词执行后若导致文档/代码变更，必须在文件中记录关联变更
- 被替代的提示词不删除，标注状态为"已废弃"并说明替代方案

---

## 3. 架构铁律（不可违反）

以下三条规则是 NanoFleet 的架构基石，**任何代码生成、重构建议、架构讨论中都不得违反**。

### 铁律 1：控制面与数据面严格分离（双流设计）

| 层面 | 职责 | 协议 | 承载内容 |
| :--- | :--- | :--- | :--- |
| **控制面 (Control Plane)** | 节点注册、心跳、配置下发、状态上报、隧道信令 | WebSocket + JSON | 结构化信令消息 |
| **数据面 (Data Plane)** | SSH 终端字节流、TCP 端口转发、文件传输 | Raw TCP / 二进制流 | 原始字节 |

**规则：**
- ✅ 控制面传输：`{"type": "HEARTBEAT", "cpu": 45.2, ...}`
- ✅ 数据面传输：SSH 协议原始字节
- ❌ **严禁**将 SSH 字节流塞入 WebSocket Binary Frame
- ❌ **严禁**在控制面通道中传输文件内容
- 原因：Python asyncio 事件循环无法高效处理大量二进制帧；Go 重构时无法复用逻辑。

### 铁律 2：契约先行 (Contract-First)

- 所有 Server ↔ Agent 通信数据结构，**必须先定义 Schema**，再编写实现代码。
- Python 端：使用 **Pydantic v2** 模型，所有字段必须有类型标注和校验规则。
- Go 端（未来）：使用 Protobuf 生成的 Struct。
- **严禁**在代码中硬编码无类型的 `dict` / `map` 作为通信数据结构。
- 数据契约文件统一放在 `schemas/` 目录。

### 铁律 3：面向接口编程（高内聚低耦合）

- 核心模块必须通过**抽象接口**调用，禁止跨模块直接引用具体实现类。
- Python 端：使用 `abc.ABC` + `@abstractmethod` 定义接口。
- Go 端（未来）：使用 Go Interface。
- 需要接口化的模块：
  - `Storage`（存储层：SQLite → 未来可换 PostgreSQL）
  - `Discovery`（发现层：mDNS → 未来可换 Consul）
  - `Tunnel`（隧道层：本地 TCP → 未来可换 QUIC）
  - `Notifier`（通知层：预留）

---

## 4. 技术栈规范

### 4.1 原型阶段 (Phase 1 — 当前)

| 层级 | 技术 | 版本要求 | 备注 |
| :--- | :--- | :--- | :--- |
| 前端 | Vue 3 + Vite + TailwindCSS + Shadcn-Vue | Vue ≥3.4, Vite ≥5 | Composition API，禁止 Options API |
| 后端 | Python + FastAPI | Python ≥3.11, FastAPI ≥0.100 | 全面使用 async/await |
| 数据校验 | Pydantic | ≥2.0 | 使用 v2 语法（`model_validator` 等） |
| 数据库 | SQLite + SQLAlchemy/SQLModel | SQLAlchemy ≥2.0 | 异步引擎 `aiosqlite` |
| WebSocket | FastAPI 内置 WebSocket | — | 控制面通信 |
| 服务发现 | zeroconf (mDNS) 或 UDP 广播 | — | 局域网自动发现 |
| CLI | Typer + Rich | Typer ≥0.9 | 终端美化输出 |
| 测试 | pytest + httpx (AsyncClient) | — | 异步测试 |
| 包管理 | Python: `uv` 或 `poetry`；Node: `pnpm` | — | 禁止使用 pip install 直接管理依赖 |

### 4.2 技术验证阶段 (Phase 2 — gRPC + 功能补全)

| 层级 | 技术 |
| :--- | :--- |
| 后端/Agent | Python (FastAPI + gRPC) |
| 通信 | gRPC (控制面) + Raw TCP (数据面) |
| CLI | Click / Typer |
| 打包 | PyInstaller / Docker |

> ⚠️ **Phase 2 允许 gRPC + Python，禁止 Go 代码。** Go 重构是 Phase 3 的事。

### 4.3 生产阶段 (Phase 3 — Golang 重构)

| 层级 | 技术 |
| :--- | :--- |
| 后端/Agent | Golang (Gin/Fiber + Gorilla WebSocket) |
| 通信 | gRPC (控制面) + Raw TCP (数据面) |
| CLI | Cobra |
| 打包 | GoReleaser / Docker |

---

## 5. 编码规范

### 5.1 Python 规范

```python
# ✅ 正确：类型标注 + Pydantic 模型 + 抽象接口
from abc import ABC, abstractmethod
from pydantic import BaseModel, Field

class HeartbeatPayload(BaseModel):
    node_id: str = Field(..., min_length=1, max_length=64)
    cpu_usage: float = Field(..., ge=0, le=100)
    memory_free_mb: int = Field(..., ge=0)
    timestamp: int = Field(..., gt=0)

class StorageInterface(ABC):
    @abstractmethod
    async def save_heartbeat(self, payload: HeartbeatPayload) -> None: ...

    @abstractmethod
    async def get_node_by_id(self, node_id: str) -> NodeInfo | None: ...
```

**规则：**
- 所有函数必须有类型标注（参数 + 返回值）。
- 所有外部输入（网络请求、文件读取、环境变量）必须经过 Pydantic 校验后才能使用。
- 异步函数使用 `async def`，禁止在异步上下文中调用阻塞 I/O（如 `time.sleep`、同步 `requests`）。
- 文件编码统一 UTF-8，行尾 LF。
- 缩进 4 空格，行宽上限 120 字符。

### 5.2 Vue / TypeScript 规范

- 使用 `<script setup lang="ts">` 语法。
- 组件命名：PascalCase（如 `NodeCard.vue`）。
- 组合式函数：`use` 前缀（如 `useNodeList.ts`）。
- API 请求统一通过 `api/` 目录下的封装函数调用，禁止在组件中直接写 `fetch`。
- 状态管理：轻量场景用 `composables`，复杂场景用 Pinia。

### 5.3 通用规则

- **注释语言**：代码注释使用**中文**（面向国内团队），但变量名/函数名/类名使用英文。
- **注释内容**：解释"为什么"而非"做什么"。核心网络通信逻辑必须包含设计决策注释。
- **命名**：
  - Python: `snake_case`（变量/函数），`PascalCase`（类），`UPPER_SNAKE_CASE`（常量）。
  - TypeScript: `camelCase`（变量/函数），`PascalCase`（类/组件/类型）。
  - CLI 命令/参数: 全小写 `kebab-case`。

---

## 6. 目录结构规范

```
nanofleet/
├── README.md                  # 面向人类的项目说明（展示窗口，不承担约束）
├── AGENTS.md                  # 👈 本文件（面向 AI Agent 的全局约束）
├── LICENSE
├── docs/                      # 📚 项目知识库（详见第 2 节索引）
│   ├── architecture.md
│   ├── api-contracts.md
│   ├── cli-spec.md
│   ├── db-schema.md
│   ├── roadmap.md
│   ├── coding-standards.md
│   ├── deployment.md
│   └── changelog.md
├── prompts/                   # 🎯 AI 提示词库（关键决策/操作记录）
│   ├── README.md              #    提示词库使用说明
│   ├── decisions/             #    架构/设计决策类
│   ├── features/              #    功能开发类
│   ├── reviews/               #    审查校验类
│   └── ops/                   #    运维/工具链/基础设施类
├── schemas/                   # 📐 数据契约定义（Pydantic 模型 / JSON Schema）
│   ├── __init__.py
│   ├── node.py                #    节点相关模型
│   ├── heartbeat.py           #    心跳相关模型
│   ├── tunnel.py              #    隧道相关模型
│   └── message.py             #    WebSocket 消息信封模型
├── server/                    # 🖥️ Server 端源码
│   ├── main.py                #    入口
│   ├── config.py              #    配置管理
│   ├── registry/              #    节点注册中心
│   ├── gateway/               #    隧道网关（数据面）
│   ├── storage/               #    存储层（实现 StorageInterface）
│   ├── api/                   #    REST + WebSocket API 路由
│   └── discovery/             #    Server 端的 mDNS 广播（让 Agent 找到自己）
├── agent/                     # 🤖 Agent 端源码
│   ├── main.py                #    入口
│   ├── config.py              #    配置管理
│   ├── discovery/             #    网络嗅探（mDNS 客户端 / UDP 监听）
│   ├── heartbeat/             #    心跳采集与上报
│   ├── tunnel/                #    反向隧道拨号器（数据面）
│   └── daemon/                #    systemd 服务管理
├── web/                       # 🌐 Vue 3 前端
│   ├── src/
│   │   ├── api/               #    API 请求封装
│   │   ├── components/        #    UI 组件
│   │   ├── composables/       #    组合式函数
│   │   ├── views/             #    页面视图
│   │   ├── stores/            #    Pinia 状态管理
│   │   └── App.vue
│   ├── index.html
│   └── package.json
├── cli/                       # ⌨️ nf CLI 工具
│   ├── __init__.py
│   ├── main.py                #    Typer app 入口
│   ├── commands/              #    子命令模块
│   │   ├── status.py
│   │   ├── ssh.py
│   │   ├── server.py
│   │   └── agent.py
│   └── utils/                 #    CLI 工具函数（配置读取、输出格式化）
├── scripts/                   # 🔧 部署与运维脚本
│   ├── install.sh             #    一键安装脚本（curl | bash）
│   ├── nanofleet-server.service  # systemd 服务文件
│   └── nanofleet-agent.service
├── tests/                     # 🧪 测试
│   ├── test_server/
│   ├── test_agent/
│   ├── test_cli/
│   └── conftest.py
└── pyproject.toml             # Python 项目配置（依赖、工具配置）
```

**规则：**
- 新增文件必须放入上述对应目录，**禁止**在根目录随意创建脚本。
- 若需新增目录，必须在 `docs/architecture.md` 中说明理由。
- `schemas/` 目录是全局共享的数据契约，server/agent/cli 均可 import，但**禁止反向依赖**。

---

## 7. 数据契约规范

### 7.1 WebSocket 消息信封

所有控制面 WebSocket 消息必须使用统一信封格式：

```python
class WSEnvelope(BaseModel):
    """WebSocket 消息统一信封"""
    type: MessageType          # 消息类型枚举
    msg_id: str                # 唯一消息 ID (UUID)
    timestamp: int             # Unix 时间戳 (秒)
    payload: dict              # 具体负载（由 type 决定 schema）
```

### 7.2 消息类型枚举

```python
class MessageType(str, Enum):
    # Agent → Server
    REGISTER = "REGISTER"
    HEARTBEAT = "HEARTBEAT"
    TUNNEL_READY = "TUNNEL_READY"

    # Server → Agent
    REGISTER_ACK = "REGISTER_ACK"
    TUNNEL_CREATE = "TUNNEL_CREATE"
    TUNNEL_CLOSE = "TUNNEL_CLOSE"
    CONFIG_UPDATE = "CONFIG_UPDATE"

    # 双向
    PING = "PING"
    PONG = "PONG"
    ERROR = "ERROR"
```

### 7.3 契约编写规则

- 每个消息类型的 `payload` 必须有对应的 Pydantic 模型。
- 模型文件放在 `schemas/` 目录，按领域分文件。
- **禁止**使用 `payload: dict = {}` 这种无类型定义。
- 新增消息类型时，必须同步更新 `docs/api-contracts.md`。

---

## 8. CLI 规范

### 8.1 基本约定

| 属性 | 值 |
| :--- | :--- |
| 命令名 | `nf` |
| 框架 | Typer (Python 原型期) → Cobra (Go 重构期) |
| 配置文件 | `~/.nanofleet/config.yaml` |
| 输出美化 | Rich (表格、颜色、进度条) |

### 8.2 命令树

```
nf
├── status              # 集群全局状态
├── ls / list           # 列出节点
├── info          # 节点详情
├── dashboard           # 打开 Web UI
├── ssh           # SSH 登录节点
├── exec     # 远程执行命令
├── scp :   # 文件传输
├── forward   [lport]  # 端口映射
├── tunnel
│   └── ls              # 列出活跃隧道
├── server
│   ├── start           # 启动 Server
│   └── stop            # 停止 Server
└── agent
    ├── start           # 启动 Agent
    ├── stop            # 停止 Agent
    └── install         # 生成部署脚本
```

### 8.3 全局 Flag

| Flag | 说明 |
| :--- | :--- |
| `--json` | 输出纯 JSON（禁止富文本），供脚本集成 |
| `--server <addr>` | 临时指定 Server 地址（覆盖配置文件） |
| `--verbose` / `-v` | 详细输出模式 |
| `--version` | 显示版本号 |

### 8.4 输出规范

- **人类模式（默认）**：使用 Rich 表格、颜色状态标记（🟢 在线 / 🔴 离线 / 🟡 异常）。
- **机器模式（`--json`）**：纯 JSON stdout，无任何装饰字符。
- **错误输出**：`stderr`，格式为 `❌ Error: <human-readable message>`，退出码非零。

---

## 9. 错误处理与日志规范

### 9.1 错误处理

- **禁止**向用户暴露 Python Traceback。
- 所有用户可见错误必须经过友好化处理。
- 网络错误必须包含重试建议或自动重试逻辑（指数退避）。
- 自定义异常层次：

```python
class NanoFleetError(Exception): ...        # 基类
class ConnectionError(NanoFleetError): ...  # 连接失败
class NodeNotFoundError(NanoFleetError): ...# 节点不存在
class AuthError(NanoFleetError): ...        # 认证失败
class TunnelError(NanoFleetError): ...      # 隧道异常
```

### 9.2 日志

- 使用 Python `logging` 模块（或 `loguru`）。
- 日志级别：
  - `DEBUG`：开发调试信息
  - `INFO`：正常业务事件（节点注册、心跳、隧道建立）
  - `WARNING`：可恢复异常（心跳超时、重连）
  - `ERROR`：不可恢复错误
- 日志格式必须包含：`[时间] [级别] [模块名] [node_id] 消息`
- Server 日志写入文件 + stdout；Agent 日志写入文件 + systemd journal。

---

## 10. 禁止事项清单

> 以下行为在 NanoFleet 项目中**严格禁止**，违反将导致代码被拒绝。

| # | 禁止事项 | 原因 |
| :-: | :--- | :--- |
| 1 | 将 SSH 字节流通过 WebSocket Binary Frame 传输 | 违反铁律 1，阻塞事件循环，Go 重构无法复用 |
| 2 | 使用无类型 `dict` 作为网络通信数据结构 | 违反铁律 2，无法校验、无法跨语言对齐 |
| 3 | 跨模块直接 import 具体实现类（绕过接口） | 违反铁律 3，耦合过紧，无法替换实现 |
| 4 | 在当前阶段编写 Go / Rust / C++ 代码 | 当前是 Python 原型期，禁止提前重构 |
| 5 | 使用 `time.sleep()` 阻塞异步事件循环 | 必须使用 `asyncio.sleep()` |
| 6 | 在 Vue 组件中直接写 `fetch()` / `axios` 调用 | 必须通过 `api/` 层封装 |
| 7 | 在根目录创建散落脚本文件 | 脚本放 `scripts/`，代码放对应模块目录 |
| 8 | 硬编码 IP 地址、端口号、密码 | 必须通过配置文件或环境变量注入 |
| 9 | 引入 Redis / MySQL / PostgreSQL / MongoDB 等外部数据库 | 原型期仅用 SQLite，保持零依赖 |
| 10 | 引入 K8s / Docker Compose 作为运行依赖 | 保持单文件可运行，容器化是可选增强 |
| 11 | 使用 `print()` 进行日志输出 | 必须使用 logging 模块 |
| 12 | 在 Pydantic v2 项目中使用 v1 语法 | 如 `validator` → 应用 `field_validator` |
| 13 | 在 `README.md` 中写入开发规范、约束规则或实现细节 | `README.md` 仅为展示窗口，约束归 `AGENTS.md` + `docs/` |
| 14 | 跳过知识库一致性校验直接修改 `README.md` | 必须先校验 `AGENTS.md` + `docs/` 体系，再同步 `README.md` |

---

## 11. 当前开发阶段与任务状态

### 当前阶段：Phase 1 — Python 原型验证（Phase 2 规划中）

**里程碑目标**：在 Web 上看到局域网内 3 台虚拟机的实时状态。

**任务优先级队列**（从高到低）：

| 优先级 | 任务 | 状态 | 说明 |
| :---: | :--- | :---: | :--- |
| P0 | 项目脚手架搭建 | 🟡 进行中 | 目录结构、pyproject.toml、基础配置 |
| P0 | 数据契约定义 | ⬜ 待开始 | `schemas/` 下所有 Pydantic 模型 |
| P1 | Server WebSocket 骨架 | ⬜ 待开始 | FastAPI + WS endpoint + 消息路由 |
| P1 | Agent 心跳上报 | ⬜ 待开始 | 定时发送 HeartbeatPayload |
| P1 | mDNS 自动发现 | ⬜ 待开始 | Agent 发现 Server |
| P2 | SQLite 存储层 | ⬜ 待开始 | 节点注册信息持久化 |
| P2 | Vue3 Dashboard | ⬜ 待开始 | 节点列表 + 状态卡片 |
| P3 | Web SSH (xterm.js) | ⬜ 待开始 | Phase 2 内容，当前阶段可提前调研 |
| P3 | 反向隧道 | ⬜ 待开始 | Phase 2 内容，当前阶段可提前调研 |

> 状态标记：✅ 已完成 | 🟡 进行中 | ⬜ 待开始 | ❌ 已取消

---

## 12. 与 Agent 协作的工作流

### 12.1 你在生成代码前必须做的事

1. **确认任务属于哪个模块**（server / agent / web / cli / schemas）。
2. **查阅对应的 `docs/` 文档**（若已存在）。
3. **检查是否违反第 10 节的禁止事项**。
4. **确认数据契约**：若涉及网络通信，先在 `schemas/` 中定义或引用已有模型。

### 12.2 代码输出格式要求

- 每次生成代码时，在代码块前注明：
  - 文件路径（如 `# File: server/registry/manager.py`）
  - 所属模块
  - 依赖的接口/契约
- 若生成多个文件，按依赖顺序排列（先接口，后实现）。
- 代码中必须包含中文注释说明关键设计决策。

### 12.3 文档修改操作流程（强制规则）

> ⚠️ **本规则为强制执行项。任何涉及文档新增、修改、删除、重命名的操作，必须严格按照以下流程执行。**

#### 触发条件

以下任一情况发生时，本流程自动触发：
- 新增 / 修改 / 删除任何 `docs/` 目录下的文档
- 新增 / 修改 / 删除任何 `prompts/` 目录下的提示词
- 新增 / 修改 `AGENTS.md` 中的任何章节
- 代码实现导致设计变更，需要同步更新文档
- 新增 / 修改数据契约、CLI 命令、数据库表结构
- 架构决策变更

#### 执行流程（严格按顺序）

```
Step 1: 检索校验知识库体系
        ┌─────────────────────────────────────────────┐
        │ 扫描 AGENTS.md 与 docs/ 目录的整体一致性：    │
        │  • AGENTS.md 第 2 节索引表是否覆盖 docs/ 全部文件 │
        │  • docs/ 中是否存在未被索引的孤立文档          │
        │  • 各文档之间的交叉引用是否仍然有效           │
        │  • 文档内容是否与当前代码实现匹配             │
        │  • prompts/ 中提示词的关联记录是否与实际变更一致 │
        └──────────────────────┬──────────────────────┘
                               ▼
Step 2: 更新知识库文档体系
        ┌─────────────────────────────────────────────┐
        │ 先完成 AGENTS.md + docs/ 的内容更新：          │
        │  • 修改/新增对应的 docs/ 详细文档              │
        │  • 若涉及全局约束变更，更新 AGENTS.md         │
        │  • 在 docs/changelog.md 中记录本次变更         │
        │  • 确保索引表、交叉引用全部对齐               │
        └──────────────────────┬──────────────────────┘
                               ▼
Step 3: 扫描校验 README.md
        ┌─────────────────────────────────────────────┐
        │ 以更新后的知识库体系为基准，检查 README.md：   │
        │  • 项目简介是否仍准确反映当前定位             │
        │  • 架构概览是否与 docs/architecture.md 一致    │
        │  • 技术栈表格是否与第 4 节规范匹配            │
        │  • 目录结构图是否与实际结构对应               │
        │  • 快速开始示例是否仍可执行                   │
        │  • 路线图是否与 docs/roadmap.md 同步           │
        │  • 文档导航表是否仍正确指向 AGENTS.md         │
        └──────────────────────┬──────────────────────┘
                               ▼
Step 4: 同步更新 README.md（仅在需要时）
        ┌─────────────────────────────────────────────┐
        │ 若 Step 3 发现不一致，则更新 README.md：      │
        │  • 仅更新"展示性"内容（简介、概览、示例）     │
        │  • 不得在 README.md 中引入约束性/规范性内容   │
        │  • 保持 README.md 的"人类友好"阅读体验        │
        │  • 确保文档导航入口仍指向 AGENTS.md           │
        └─────────────────────────────────────────────┘
```

#### 核心原则

| 原则 | 说明 |
| :--- | :--- |
| **知识库优先** | 永远先更新 `AGENTS.md` + `docs/`（约束源），再校验/更新 `README.md`（展示窗） |
| **README 不承担约束** | `README.md` 中禁止出现开发规范、编码约束、架构铁律等内容；这些归 `AGENTS.md` 管辖 |
| **README 是摘要不是全文** | `README.md` 中的架构描述、技术栈列表等是"概要展示"，详细定义以 `docs/` 为准 |
| **单向同步** | 信息流向：`docs/` → `AGENTS.md`（摘要）→ `README.md`（展示）。禁止反向 |
| **变更可追溯** | 每次文档变更必须在 `docs/changelog.md` 中记录：时间、变更内容、原因 |

#### 校验清单（Agent 自检用）

完成文档修改后，Agent 必须逐项确认：

- [ ] `AGENTS.md` 第 2 节索引表是否覆盖了 `docs/` 下的所有文件？
- [ ] `docs/` 中是否存在未被 `AGENTS.md` 索引的"孤儿文档"？
- [ ] 本次修改的文档是否已在 `docs/changelog.md` 中记录？
- [ ] `README.md` 中的目录结构图是否与实际一致？
- [ ] `README.md` 中是否误引入了约束性/规范性内容？
- [ ] `README.md` 的文档导航是否仍正确指向 `AGENTS.md`？
- [ ] 各文档间的交叉引用链接是否仍然有效？
- [ ] `prompts/` 中被本次变更影响的提示词是否已更新关联记录？

### 12.4 不确定时的处理

- 若任务描述模糊，**先提问确认**，不要猜测。
- 若设计存在多种方案，列出 2~3 个选项并说明 trade-off，等待人类决策。
- 若发现本文件中的规则与用户即时指令冲突，以**用户即时指令**为准，但需提醒用户注意冲突。

---

## 13. 术语表

| 术语 | 含义 |
| :--- | :--- |
| **Server** | NanoFleet 中央控制节点，运行 Registry + Gateway + Web UI |
| **Agent** | 部署在每台被管服务器上的守护进程 |
| **控制面** | 负责信令通信的逻辑层（WebSocket + JSON） |
| **数据面** | 负责字节流传输的逻辑层（Raw TCP） |
| **Node** | 集群中的一台服务器（由 Agent 管理） |
| **Registry** | Server 中负责节点注册与生命周期管理的模块 |
| **Tunnel** | Agent 与 Server 之间的反向 TCP 连接，用于穿透 NAT |
| **Heartbeat** | Agent 定时上报的状态数据包 |
| **Discovery** | Agent 在局域网内自动寻找 Server 的机制（mDNS/UDP） |
| **nf** | NanoFleet 的 CLI 命令名 |
| **契约 (Contract)** | Server 与 Agent 之间通信的数据结构定义（Schema） |
| **知识库体系** | `AGENTS.md` + `docs/` 目录构成的完整约束与设计文档集合 |
| **展示窗口** | `README.md`，面向人类的项目介绍入口，不承担约束功能 |

---

<p align="center">
  <sub>⚡ 本文件由项目维护者编写，最后更新：2026-08-07。任何修改需经维护者确认。</sub>
</p>