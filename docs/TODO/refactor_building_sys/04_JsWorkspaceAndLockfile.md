---
title: 步骤 04：pnpm workspace 与锁文件
status: draft
document_type: implementation-step
track: foundation
step: 4
depends_on:
  - 02_PixiToolchainFoundation.md
  - 27_DependencyGovernance.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 04：pnpm workspace 与锁文件

[上一步：流水线阶段契约](./03_PipelineStageContracts.md) · [返回总计划](./index.md) · [下一步：外部制品清单](./05_ExternalArtifactManifest.md)

## 旧实现情况

- 根项目、Web Chat、MCP bridge 与 examples 使用多套 npm/pnpm 调用
- workspace packages 未显式列出，lockfile 未形成唯一输入
- 某些构建脚本依赖从其他 package 偶然可见的模块

## 预期的新实现情况

- pnpm 建议使用 `11.13` 或更高版本，根 `packageManager` 与当前 Pixi 求解结果记录同一精确版本
- 根 `pnpm-lock.yaml` 覆盖所有仓库自有 JavaScript package
- Node/TypeScript 脚本保留在所属 package 内
- pnpm 不承担 Android、APK、制品或流水线编排
- 依赖更新按检查报告主动推进，必要时同步修改源码和 package scripts

## 修改作用域

- 根与受管理 package 的 `package.json`
- `pnpm-workspace.yaml`
- `pnpm-lock.yaml`
- `.gitignore`
- JavaScript package 文档

## 计划

1. 盘点根 package、Web Chat、MCP bridge 和确需构建的 examples。
2. 明确纳入和排除理由，不使用覆盖全仓库的 workspace 通配符。
3. 修正每个 package 的直接依赖声明。
4. 统一 package script 中的 pnpm 调用。
5. 增加静态检查，拒绝受管理目录中的其他包管理器锁文件。
6. 将 pnpm outdated、安全通告、直接与传递依赖路径和 peer dependency 约束接入 `dependency_update_check` 报告。
7. 更新依赖时同步处理外部 API 与行为变化，并在验证后提交新 lockfile。
8. 为步骤 32 提供 pnpm 更新、冻结安装、依赖图和 JS assets 对比接口；更新会话、人工测试和提交范围由步骤 32 统一编排。
9. 确认后同步更新全部受管理 JavaScript package 和根 `pnpm-lock.yaml`，不留下未说明的旧基线。

## 验收

- workspace 范围与清单一致
- 受管理 package 只有一个 lockfile
- package script 不调用 npm 或 yarn
- JavaScript 构建不创建通用 Android/CI 工具
- 冻结安装不会修改 lockfile
- 依赖检查报告能够区分可直接更新项、需要源码适配项和安全相关项
- pnpm 已公布安全漏洞会标记为 release 阻塞项，普通版本更新只标记警告
- release 构建记录 `pnpm-lock.yaml` hash
- 暂缓更新的 package 具有原因、负责人和期限
- 脚本报告解释依赖图、构建产物和 assets 差异；行为差异由人工测试记录解释
- pnpm 自身升级触发的 bootstrap 和继续命令符合步骤 32 的统一检查点协议

## 文档同步

- 更新各 package README 和正式构建文档

## 完成记录

状态：未开始。
