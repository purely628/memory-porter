# Changelog

## 1.0.0 — 2026-09-29

首个公开发布版本。

- 五个动作：save（沉淀）/ recall（恢复）/ migrate（迁移进 AGENTS.md 受管区块）/ export（导出自执行提示包）/ forget（遗忘）
- 记忆库按「通用 common/ + 各项目目录」分层，项目隔离，互不污染
- 提示包自带「给读取本文件的 Agent」指令头，脱离技能、任何 AI 读取即生效
- 导出提示包命名规范：`<项目>-<范围>-提示包-<YYYYMMDD>.md`
- 首次运行自动初始化记忆库骨架（INDEX.md / topics/common / exports）
- 中英双语触发词；支持任意 Agent Skills 标准客户端（ZCode、Claude Code 等）
