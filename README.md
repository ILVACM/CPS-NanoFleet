# NanoFleet

> **NanoFleet — A nano-sized fleet commander for your lab servers.**
>
> 纳米舰队：面向受限局域网环境的轻量级服务器集群管理与 SSH 堡垒机系统。

<!-- Badges (待项目成熟后补充) -->
<!-- ![License](https://img.shields.io/badge/license-MIT-blue.svg) -->
<!-- ![Status](https://img.shields.io/badge/status-prototype-orange.svg) -->

---

## ⚠️ 文档导航（请先阅读）

| 你是谁？ | 请前往 |
| :--- | :--- |
| **人类开发者 / 访客** | 你正在看的这份 `README.md` 就是为你准备的，请继续往下阅读。 |
| **AI Agent / LLM 辅助工具** | **请立即跳转至 [`AGENTS.md`](./AGENTS.md)**，那里包含本项目的完整上下文、架构约束、编码规范和任务指引。`AGENTS.md` 内部已建立对 `./docs/` 文档目录与 `./prompts/` 提示词库的完整索引。 |

> **设计意图**：本仓库采用双入口文档体系。`README.md` 面向人类阅读理解；`AGENTS.md` 面向 AI Agent（包括但不限于 Trae、MimoCode、Cursor、Continue、Copilot Workspace 等），作为全局约束与知识库的唯一权威入口。无论 Agent 工具的索引策略如何配置（默认读取 `AGENTS.md`、先读取 `README.md`、或通过自身配置文件指定），最终都应收敛到 `AGENTS.md` 作为上下文起点。

---

## 📖 项目简介

### 背景与痛点

在高校实验室、中小企业机房、家庭 HomeLab 等场景中，服务器集群管理面临一系列"恶劣"网络环境挑战：

- **无固定 IP**：校园网采用 DHCP 动态分配，节点重启后 IP 变化。
- **网络隔离与限制**：禁止私自搭建 Web 服务，限制特定端口，存在 DPI 深度包检测。
- **重装频繁**：学生流动性大，节点经常重装系统，要求管理工具"免配置、U 盘一键部署"。
- **现有方案不适用**：JumpServer / Teleport 过于臃肿；frp / ngrok 仅解决穿透缺乏统一管理；RustDesk / ToDK 是 GUI 远程桌面不适合纯命令行服务器集群。

### NanoFleet 是什么

NanoFleet 是一款 **极度轻量、零配置、自动发现** 的局域网服务器集群管理系统：

- 🪶 **单文件部署**：无外部数据库依赖（内嵌 SQLite），一个二进制跑起来就能用。
- 🔍 **自动发现**：节点启动后通过 mDNS/UDP 广播自动寻找 Server 并注册，无需手动配置 IP。
- 🖥️ **Web 可视化**：现代化 Dashboard 实时监控节点状态、资源使用率。
- 🔐 **SSH 堡垒机**：通过反向隧道实现 Web 端一键 SSH 登录任意节点，无需记忆 IP 和端口。
- 📦 **平滑演进**：Python 原型验证 → Golang 生产重构，架构契约不变。

---

## 🏗️ 系统架构概览

```
┌─────────────────────────────────────────────────────────────────┐
│                        NanoFleet Server                         │
│  ┌───────────┐  ┌──────────────┐  ┌───────────┐  ┌──────────┐ │
│  │  Registry │  │Tunnel Gateway│  │  Web UI   │  │ Storage  │ │
│  │ 节点注册  │  │  隧道网关    │  │ Dashboard │  │ SQLite   │ │
│  └─────┬─────┘  └──────┬───────┘  └───────────┘  └──────────┘ │
│        │               │                                       │
│   控制面(WebSocket)  数据面(Raw TCP)                            │
└────────┼───────────────┼───────────────────────────────────────┘
         │               │
    ┌────┴───────────────┴────┐
    │      Network (LAN)      │
    └────┬───────────────┬────┘
         │               │
┌────────┴───┐    ┌──────┴─────┐
│  Agent #1  │    │  Agent #N  │
│ (子节点)   │    │  (子节点)  │
└────────────┘    └────────────┘
```

> **核心设计铁律**（控制面/数据面分离、契约先行、面向接口编程）的权威定义详见 [`AGENTS.md`](./AGENTS.md) §3。

---

## 🛠️ 技术栈

> 技术栈的权威定义详见 [`AGENTS.md`](./AGENTS.md) §4。此处仅展示概要。

| 阶段 | 后端 | 前端 | 数据库 | 通信 |
| :--- | :--- | :--- | :--- | :--- |
| **Phase 1**（当前） | Python + FastAPI | Vue 3 + Vite + TailwindCSS | SQLite | WebSocket + Raw TCP |
| **Phase 2/3**（规划） | Golang | — | — | gRPC + Raw TCP |

---

## 📂 项目目录结构

```
nanofleet/
├── README.md                  # 👈 你在这里（面向人类的说明文件）
├── AGENTS.md                  # 🤖 AI Agent 全局约束与知识库入口
├── docs/                      # 📚 项目知识库文档目录
│   ├── architecture.md        #    架构设计
│   ├── api-contracts.md       #    接口与数据契约
│   ├── cli-spec.md            #    CLI 命令规范
│   ├── db-schema.md           #    数据库表结构
│   ├── roadmap.md             #    开发路线图
│   ├── coding-standards.md    #    编码规范细则
│   ├── deployment.md          #    部署方案
│   └── changelog.md           #    变更日志
├── prompts/                   # 🎯 AI 提示词库（关键决策/操作记录）
├── server/                    # Server 端源码
├── agent/                     # Agent 端源码
├── web/                       # Vue 3 前端源码
├── cli/                       # nf CLI 工具源码
├── schemas/                   # 数据契约定义 (Pydantic / JSON Schema)
├── scripts/                   # 部署脚本、安装脚本
└── LICENSE
```

---

## 🚀 快速开始

> 🚧 **当前状态**：项目处于原型验证阶段 (Phase 1)，以下为规划中的使用方式。

```bash
# 1. 启动 Server（控制节点）
nf server start --port 8000

# 2. 在子节点部署 Agent（一行命令）
curl -sSL http://<server-ip>:8000/install.sh | bash

# 3. 查看集群状态
nf ls

# 4. 一键 SSH 登录任意节点
nf ssh lab-node-01

# 5. 打开 Web 控制台
nf dashboard
```

---

## 🗺️ 开发路线图

> 详细路线图与任务状态详见 [`docs/roadmap.md`](./docs/roadmap.md) 与 [`AGENTS.md`](./AGENTS.md) §11。

| 阶段 | 目标 |
| :--- | :--- |
| **Phase 1**: Python 原型 | FastAPI + WebSocket、mDNS 自发现、心跳上报、Vue3 Dashboard |
| **Phase 2**: 隧道与终端 | 反向隧道、xterm.js Web SSH、CLI 工具 |
| **Phase 3**: Go 重构 | Golang 重写、高性能转发、v1.0.0 发布 |

---

## 📜 许可证

MIT License（待正式开源时补充）

---

## 🙏 致谢与灵感

- [Teleport](https://goteleport.com/) — 企业级零信任 SSH 平台（NanoFleet 的"精神导师"）
- [frp](https://github.com/fatedier/frp) — 高性能内网穿透（隧道机制参考）
- [Pydantic](https://docs.pydantic.dev/) — 数据校验与契约基石
- [FastAPI](https://fastapi.tiangolo.com/) — 现代 Python Web 框架

---

<p align="center">
  <sub>Built with 🧪 in a university lab. Powered by curiosity and caffeine.</sub>
</p>
