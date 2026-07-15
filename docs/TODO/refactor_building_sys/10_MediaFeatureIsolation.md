---
title: 步骤 10：媒体 feature 隔离
status: draft
document_type: implementation-step
track: android-architecture
step: 10
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

# 步骤 10：媒体 feature 隔离

[上一步：能力架构决策](./09_CapabilityArchitectureDecision.md) · [返回总计划](./index.md) · [下一步：语音 feature 隔离](./11_SpeechFeatureIsolation.md)

## 旧实现情况

- FFmpeg 类型、执行逻辑、二进制、资源和许可证没有单一 module 所有者

## 预期的新实现情况

- `:feature:media` 拥有媒体实现、依赖、资源、native 制品和许可证
- 调用方只依赖步骤 9 确认的 capability contract

## 修改作用域

- media capability 调用方
- `feature/media/`
- 对应 Gradle、Manifest、资源、制品和许可证

## 计划

1. 在 `:app` 内先将 FFmpeg 直接依赖收束到实现边界。
2. 创建 media library module 和精确依赖。
3. 移动实现、资源与制品所有权。
4. 更新 registry 组合，不改变用户入口。
5. 静态检查反向依赖和重复资源。

## 验收

- 基础调用方不 import FFmpeg 实现类型
- media 制品只有一个 Gradle 消费者
- 全量组合的媒体行为满足步骤 1 契约
- 错误路径具有明确日志

## 文档同步

- 更新 module 与制品所有权文档

## 完成记录

状态：等待步骤 5、9 和 36。
