---
title: 步骤 09：能力架构决策
status: draft
document_type: architecture-decision-step
track: android-architecture
step: 9
depends_on:
  - 01_BaselineAndUserspaceContracts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: user_accepts_module_boundary_decision
    status: requires_user_decision
    blocks:
      - step_10
      - step_11
      - step_12
      - step_28
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 09：能力架构决策

[上一步：Gradle 工具链规范化](./08_GradleToolchainNormalization.md) · [返回总计划](./index.md) · [下一步：媒体 feature 隔离](./10_MediaFeatureIsolation.md)

## 旧实现情况

- `:app` 调用方直接依赖 FFmpeg、Sherpa、models、subpack 和具体导出实现
- 当前计划预设 media、speech、export 三个 feature，但缺少正式边界决策

## 预期的新实现情况

- 先确认能力拆分目标和不拆分范围，再移动代码
- 契约定义输入、输出、状态、错误、线程和生命周期
- 明确允许依赖的 core、data、UI 与平台模块
- 全量组合继续满足步骤 1 的用户空间契约

## 修改作用域

- 本步骤 ADR
- capability API 草案
- module 依赖规则

本步骤不移动源码、资源或制品。

## 计划

1. 盘点媒体、语音和导出的调用方、实现、资源、native 与数据格式。
2. 记录选择这三个边界的理由和明确排除项。
3. 定义 capability contract 与 registry 组合方式。
4. 列出允许依赖和禁止依赖方向。
5. 由用户确认架构决策后再开始三个迁移步骤。

## 验收

- 每个边界都有真实调用方和所有者证据
- 契约不泄漏具体 SDK、asset 路径或 native 类型
- 迁移顺序不会要求一次移动三个 feature
- 用户空间比较基线明确指向步骤 1

## 文档同步

- 新增正式架构决策文档

## 完成记录

状态：等待架构决策确认。
