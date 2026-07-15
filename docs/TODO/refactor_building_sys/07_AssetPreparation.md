---
title: 步骤 07：资产准备入口
status: draft
document_type: implementation-step
track: foundation
step: 7
depends_on:
  - 03_PipelineStageContracts.md
  - 04_JsWorkspaceAndLockfile.md
  - 06_ToolingBoundaryMigration.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 07：资产准备入口

[上一步：工具职责边界迁移](./06_ToolingBoundaryMigration.md) · [返回总计划](./index.md) · [下一步：Gradle 工具链规范化](./08_GradleToolchainNormalization.md)

## 旧实现情况

- Web Chat、ToolPkg、MCP bridge 和其他 assets 使用不同命令生成
- 生成结果没有统一的路径、大小、hash 和生产 task 清单
- JavaScript 构建与跨 package 文件同步边界不清晰

## 预期的新实现情况

- Pixi 为每类资产提供单一职责 task
- package 内构建继续由 pnpm 执行
- Python 负责跨 package 清单、hash 和同步检查
- `js-assets` manifest 记录生产 task、lockfile hash、输入和输出

## 修改作用域

- `ci/script/assets/`
- `ci/pixi.toml`
- JS assets schema 与 manifest
- 各 JavaScript package 的 scripts

## 计划

1. 定义 `js_install`、`js_webchat`、`js_toolpkg`、`js_bridge` 与 `js_assets_check` task。
2. package task 只构建所属产品，不直接调用 Gradle。
3. Python 入口计算输出文件大小和 SHA-256，并生成稳定排序的 manifest。
4. 检查生成结果、Git 工作树和 manifest 的差异。
5. 将 task 映射到步骤 3 的 `dependency_prepare` 和 `asset_prepare` 阶段。

## 验收

- 每个 asset 具有唯一生产 task 和消费者
- Node.js 不处理 APK、Android SDK、外部制品或流水线 profile
- Gradle 不安装 pnpm 依赖，也不启动资产构建
- 文件缺失或 hash 不一致会明确终止对应阶段
- 资产命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 `ci/README.md` 和各 package README

## 完成记录

状态：未开始。
