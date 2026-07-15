---
title: 步骤 11：语音 feature 隔离
status: draft
document_type: implementation-step
track: android-architecture
step: 11
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

# 步骤 11：语音 feature 隔离

[上一步：媒体 feature 隔离](./10_MediaFeatureIsolation.md) · [返回总计划](./index.md) · [下一步：导出 feature 隔离](./12_ExportFeatureIsolation.md)

## 旧实现情况

- 语音服务直接管理 Sherpa 模型路径、native 初始化和设置入口
- models、源码 native 与预编译库的所有权不集中

## 预期的新实现情况

- `:feature:speech` 拥有语音实现、models、Sherpa 构建、native 输入和许可证
- 调用方只依赖语音 capability contract

## 修改作用域

- speech capability 调用方
- `feature/speech/`
- 语音服务、设置、models、CMake、JNI 与许可证

## 计划

1. 在 `:app` 内收束模型路径、初始化和服务生命周期。
2. 创建 speech library module。
3. 移动实现、models 与 native 构建所有权。
4. 明确源码目标与预编译库的选择，不允许重复提供者。
5. 更新 registry 和用户设置入口。

## 验收

- 基础调用方不包含 Sherpa 类型和 models 路径
- speech module 是相关模型与 native 目标的唯一所有者
- 全量组合的语音行为满足步骤 1 契约
- 初始化和运行错误完整记录

## 文档同步

- 更新模型、native 与 module 所有权文档

## 完成记录

状态：等待步骤 5、9 和 36。
