---
title: 步骤 12：导出 feature 隔离
status: draft
document_type: implementation-step
track: android-architecture
step: 12
depends_on:
  - 05_ExternalArtifactManifest.md
  - 09_CapabilityArchitectureDecision.md
  - 36_LocalArtifactInputNormalization.md
decision_status: pending_dependency
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 12：导出 feature 隔离

[上一步：语音 feature 隔离](./11_SpeechFeatureIsolation.md) · [返回总计划](./index.md) · [下一步：Capability 变体组合](./28_CapabilityVariantComposition.md)

## 旧实现情况

- 导出 UI、Android/Windows 实现、subpack 路径和资源由 `:app` 共同持有

## 预期的新实现情况

- `:feature:export` 拥有导出实现、subpack、资源和许可证
- 聊天、工具箱和导航只依赖导出 capability contract

## 修改作用域

- export capability 调用方
- `feature/export/`
- 导出 UI、实现、subpack、资源与许可证

## 计划

1. 在 `:app` 内收束导出状态、错误和平台实现边界。
2. 创建 export library module。
3. 移动实现、UI 资源和 subpack 所有权。
4. 更新 registry、导航与工具注册。
5. 检查导出数据格式和用户可见结果。

## 验收

- 基础调用方不包含 subpack 路径或平台导出实现
- export module 是相关资源和制品的唯一所有者
- 既有导出格式和用户结果满足步骤 1 契约
- Android 与 Windows 导出错误均有日志

## 文档同步

- 更新导出格式与 module 所有权文档

## 完成记录

状态：等待步骤 5、9 和 36。
