---
title: 提交与代码风格
tags: [git, typescript, style]
source: demo-web-app
updated: 2026-09-29
applies: demo-web-app 项目内所有写代码、提交代码的任务
---

## 约定

- 包管理器用 pnpm，不用 npm/npx（为什么：团队锁了 pnpm-lock.yaml）。
- TypeScript 开 strict；禁止 any，必要时用 unknown + 类型收窄。
- 提交信息中文，格式 `<type>: <摘要>`，type ∈ feat/fix/docs/refactor/test。
- 提交前必须通过 `pnpm lint && pnpm test`，失败不许提交。

## 代码模板

```bash
pnpm lint && pnpm test && git commit -m "feat: 新增xx功能"
```
