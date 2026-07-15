---
title: 步骤 35：QA Release 与 Lite 运行验证
status: draft
document_type: implementation-step
track: apk-and-qa
step: 35
depends_on:
  - 21_QaFixtureFoundation.md
  - 28_CapabilityVariantComposition.md
  - 30_CommonAndQaApkAuditPolicies.md
  - 31_BuildPipelineProfiles.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: qa_variant_apks
    status: pending_runtime_evidence
    blocks:
      - qa_variant_runtime_verification
  - id: qa_device_or_emulator
    status: pending_runtime_evidence
    blocks:
      - qa_variant_runtime_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 35：QA Release 与 Lite 运行验证

[上一步：公告弹窗离线场景](./24_QaAnnouncementOffline.md) · [返回总计划](./index.md) · [下游：QA 与 Release 流水线](./33_QaReleasePipelineProfiles.md)

## 旧实现情况

- 三个离线场景只覆盖 QA debug
- `qaFullRelease` 只有配置和 APK 内容断言，没有 official-test 运行验证
- `qaLiteDebug` 只有依赖与打包断言，没有用户可见能力边界验证

## 预期的新实现情况

- `qaFullRelease` 使用不随 APK 发布的测试编排验证 official-test、登录入口和 release 网络边界
- `qaLiteDebug` 验证 full capability 不可用时的明确 UI 与行为
- 运行验证不向 release APK 添加 debug receiver、fixture endpoint 或调试依赖
- 每个 variant 独立报告构建清单、APK hash、设备和结果

## 修改作用域

- `ci/script/qa/` 的 variant smoke 编排
- Android instrumentation test APK 或外部 UI 编排
- QA variant runtime report schema

## 计划

1. 为 `qaFullRelease` 定义 official-test、启动、登录入口、网络配置和核心用户路径 smoke 集合。
2. 使用独立测试 APK 或外部设备编排驱动 `qaFullRelease`，不修改待发布 APK 内容。
3. 为 `qaLiteDebug` 定义媒体、语音、导出能力缺失时的 UI、导航、错误和依赖边界。
4. 对照 `qaFullDebug` 记录同一基础用户路径的 variant 差异。
5. 输出逐 variant 状态、APK hash、构建清单、设备信息和诊断证据。

## 验收

- `qaFullRelease` 在无 debug endpoint 条件下完成 official-test smoke
- 测试编排不进入 release APK
- `qaLiteDebug` 不加载 full 实现、native 库、models 或 subpack
- lite 用户界面与错误语义符合步骤 28 定义
- 三个 QA variant 状态分别报告
- 命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 QA release、lite 行为和独立测试编排说明

## 完成记录

状态：等待 QA variants、设备、common/QA policy 和步骤 31 build profile。
