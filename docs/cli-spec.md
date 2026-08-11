# CLI 命令规范

> 本文档索引 `nf` CLI 的命令规范。核心约定详见 [`AGENTS.md`](../AGENTS.md) §8 CLI 规范。

---

## 1. 基本约定

命令名、框架、配置文件、输出美化方式详见 [`AGENTS.md`](../AGENTS.md) §8.1。

---

## 2. 命令树

完整命令树详见 [`AGENTS.md`](../AGENTS.md) §8.2。当前规划命令：

- `status` / `ls` / `info` — 节点状态查询
- `dashboard` — 打开 Web UI
- `ssh` / `exec` / `scp` / `forward` — 远程操作
- `tunnel ls` — 隧道管理
- `server start|stop` — Server 生命周期
- `agent start|stop|install` — Agent 生命周期

<!-- TODO: 待各子命令实现后补充详细参数定义 -->

---

## 3. 全局 Flag

详见 [`AGENTS.md`](../AGENTS.md) §8.3。

---

## 4. 各子命令详细规范

<!-- TODO: 待子命令实现后按以下模板逐个补充：
- 用途
- 用法
- 参数
- 选项
- 输出示例（人类模式 + --json 模式）
- 退出码 -->

---

## 5. 输出规范

人类模式与机器模式（`--json`）的输出约定详见 [`AGENTS.md`](../AGENTS.md) §8.4。

<!-- TODO: 待 Rich 表格样式确定后补充各命令的输出格式示例 -->