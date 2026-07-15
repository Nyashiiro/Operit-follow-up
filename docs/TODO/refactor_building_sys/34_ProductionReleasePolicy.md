---
title: 步骤 34：Production Release Policy
status: draft
document_type: implementation-step
track: variants-and-security
step: 34
depends_on:
  - 14_DebugReleaseBoundary.md
  - 15_SigningVersionAndUpdateCompatibility.md
  - 19_ApkAudit.md
  - 28_CapabilityVariantComposition.md
  - 29_OAuthSecretMigration.md
  - 30_CommonAndQaApkAuditPolicies.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: production_signed_target_apk
    status: pending_runtime_evidence
    blocks:
      - prodFullRelease.package_audit_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 34：Production Release Policy

[上一步：OAuth Secret 迁移](./29_OAuthSecretMigration.md) · [返回总计划](./index.md) · [下游：QA 与 Release 流水线](./33_QaReleasePipelineProfiles.md)

## 旧实现情况

- production 更新链、OAuth secret absence 与 QA policy 曾绑定在同一步骤
- production 私钥与签名目标 APK 门禁会传递到 QA policy 和 QA 补丁

## 预期的新实现情况

- `prodFullRelease` 拥有独立的 applicationId、版本、证书、网络、OAuth 和内容 policy
- policy 可以使用 baseline 公钥证书摘要实现
- production 私钥验证、OAuth 部署和目标 APK 只阻塞 production release 相关验证与 stage
- QA policy、QA 补丁与 build profile 不依赖本步骤

## 修改作用域

- `ci/config/build-system/apk-audit/variants/prod-full-release/`
- production policy schema 扩展
- release profile 的 production gate 映射

## 计划

1. 绑定 production applicationId、capability、build type、version 配置和构建清单 hash。
2. 校验 baseline 证书摘要、证书 lineage 与 versionCode 严格递增。
3. 声明 release 禁止出现的 debug 类、action、组件、assets、依赖和本机 endpoint。
4. 接入步骤 29 的 secret 名称、已知值 hash、配置 key 与相关字符串不存在断言。
5. 绑定 production 服务、redirect URI 和网络配置 policy。
6. 将 production policy 结果提供给步骤 33 的 release report、patch_prepare 与 publish。

## 验收

- production applicationId、签名 lineage 或 versionCode 不满足更新链时失败
- release APK 出现 debug 内容、client secret 或非 production endpoint 时失败
- 私钥不进入审计环境；签名判断只使用 APK、证书与公开 baseline
- 私钥验证暂缓时 policy 仍可实现，`prodFullRelease` 验证与 production 发布保持阻塞
- production 门禁不影响 QA policy、QA 补丁和普通 build
- 审计报告绑定 APK hash、构建清单 hash 和 policy 版本

## 文档同步

- 更新 production APK、OAuth、签名更新链和发布门禁说明

## 完成记录

状态：policy 可先实现；OAuth cutover、私钥匹配与 production 目标 APK 分别阻塞对应 release 动作。
