---
title: 步骤 30：Common 与 QA APK 审计策略
status: draft
document_type: implementation-step
track: apk-and-qa
step: 30
depends_on:
  - 13_VariantMatrixAndArtifacts.md
  - 14_DebugReleaseBoundary.md
  - 16_DebugCommandRegistry.md
  - 19_ApkAudit.md
  - 28_CapabilityVariantComposition.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: qa_release_signing_material
    status: pending_external
    blocks:
      - qaFullRelease.package_audit_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 30：Common 与 QA APK 审计策略

[上一步：APK 审计](./19_ApkAudit.md) · [返回总计划](./index.md) · [下游：Production Release Policy](./34_ProductionReleasePolicy.md)

## 旧实现情况

- APK 内容检查与底层 ZIP、Manifest、签名读取混在一个实现步骤
- QA debug、QA release 和 full/lite 缺少分别版本化的允许集合
- debug 入口存在性与 release 调试内容不存在性没有统一映射到 `package_audit`

## 预期的新实现情况

- 步骤 19 生成通用 inventory，本步骤应用 common 与 QA policy
- `qaFullDebug`、`qaLiteDebug` 和 `qaFullRelease` 分别绑定 identity、签名角色、组件、权限、网络、ABI、assets、DEX 标记和 native 库规则
- QA debug 必须包含步骤 16 批准的调试入口
- `qaFullRelease` 必须证明调试内容和 fixture endpoint 不存在
- production applicationId、OAuth secret 与 production 更新链由步骤 34 管理

## 修改作用域

- `ci/config/build-system/apk-audit/common/`
- `ci/config/build-system/apk-audit/variants/qa-*/`
- common/QA audit policy schema
- `package_audit` 阶段配置

## 计划

1. 建立通用 identity、capability、build type、构建清单与 policy 绑定规则。
2. 为 `qaFullDebug`、`qaLiteDebug` 和 `qaFullRelease` 建立版本化 policy。
3. 为 QA debug 声明必须存在的 receiver、action、具名命令和调试资产。
4. 为 QA release 声明禁止出现的调试类、action、组件、字符串、assets、依赖和本机 endpoint。
5. 为 full 与 lite 分别声明允许的 feature assets、native 库、ABI 和依赖证据。
6. 为 QA release 声明 QA 身份、QA release 签名和固定 official-test 配置。
7. 将 policy 判定映射到 `package_audit`，输出 inventory、命中规则、证据位置和状态。

## 验收

- 三个 QA 变体分别选择唯一 policy，未知 QA 变体直接失败
- QA debug 缺少批准调试入口时失败
- `qaFullRelease` 出现 debug receiver、通用执行入口、debug assets、fixture endpoint 或 debug 依赖时失败
- `qaLiteDebug` 出现 full feature 内容时失败
- QA release 签名材料只阻塞 `qaFullRelease` 的审计验证，不阻塞 policy 实现或 QA debug
- 审计报告与 APK hash、构建清单 hash 和 policy 版本绑定
- 命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 common 与三个 QA 变体的 APK 内容和安全边界

## 完成记录

状态：等待 capability 组合和 QA debug 命令注册；QA release 签名只阻塞对应验证。
