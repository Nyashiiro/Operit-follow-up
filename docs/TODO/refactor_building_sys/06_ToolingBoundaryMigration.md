---
title: 步骤 06：工具职责边界迁移
status: draft
document_type: implementation-step
track: foundation
step: 6
depends_on:
  - 02_PixiToolchainFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 06：工具职责边界迁移

[上一步：外部制品清单](./05_ExternalArtifactManifest.md) · [返回总计划](./index.md) · [下一步：资产准备入口](./07_AssetPreparation.md)

## 旧实现情况

- `tools/` 同时包含仓库工作流、宿主工具、应用内置程序和调试工具
- Python、Batch、Shell 与 Node package 的调用方通过旧路径耦合
- 全目录迁移会制造与构建重构无关的大量路径改动

## 预期的新实现情况

- 只迁移本计划实际使用的工具
- `ci/script/<意图>/` 保存 Python 工作流
- `ci/tools/<名称>/` 保存宿主辅助工程和二进制
- `tools_built-in/<名称>/` 保存随应用交付或生成应用内置内容的工具
- JavaScript package 内脚本继续跟随所属 package

## 修改作用域

- 本计划实际引用的 `tools/` 条目
- `ci/script/`
- `ci/tools/`
- `tools_built-in/`
- 对应调用方与文档

## 计划

1. 为待迁移条目记录旧路径、职责、运行环境、调用方和产物。
2. 逐个功能组迁移，并在同一修改中更新所有调用方。
3. 保持脚本业务算法、参数和产物协议不变。
4. MCP bridge、Web Chat 等 package 内 Node 脚本不改造成仓库工作流。
5. 不属于本计划的开发工具另建 TODO，不在本步骤搬动。

## 验收

- 每个迁移项具有完整调用方清单
- 新目录能够由职责判断，不按扩展名分类
- 旧路径没有活动引用
- 迁移前后输入和输出协议一致
- 被迁移或修改的 CI 命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新工具目录说明和各功能 README

## 完成记录

状态：未开始。
