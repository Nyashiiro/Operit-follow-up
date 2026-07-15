---
title: 步骤 15：签名、版本与更新兼容
status: draft
document_type: implementation-step
track: variants-and-security
step: 15
depends_on:
  - 01_BaselineAndUserspaceContracts.md
  - 13_VariantMatrixAndArtifacts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: production_private_key_match
    status: deferred_by_user
    blocks:
      - prodFullRelease.android_assemble
      - prodFullRelease.package_audit_verification
      - production.patch_publish
      - publish
    reminder: required_at_release_prepare
  - id: qa_release_signing_material
    status: pending_external
    blocks:
      - qaFullRelease.android_assemble
      - qaFullRelease.package_audit_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 15：签名、版本与更新兼容

[上一步：Debug 与 Release 边界](./14_DebugReleaseBoundary.md) · [返回总计划](./index.md) · [下一步：QA Debug 命令注册表](./16_DebugCommandRegistry.md)

## 旧实现情况

- versionCode 与 versionName 固定在 app Gradle 文件
- nightly 使用 debug 签名，身份和更新通道含义混合
- baseline APK 已确认，证书摘要已知；用户决定暂不管理或验证对应 production 私钥

## 预期的新实现情况

- 独立版本配置是 applicationId、versionCode 与 versionName 的事实源
- QA debug、QA release 与 prod release 使用明确且不同的签名映射
- production 更新证书与已发布基线兼容
- 正式发布 versionCode 严格高于已发布基线 44
- 签名材料不进入仓库或构建报告
- 私钥匹配验证是本步骤的显式 release 阻塞项，不阻塞 baseline、通用 APK 审计、QA 或其他重构步骤

## 修改作用域

- 独立版本配置及 schema
- Gradle 版本消费
- QA 与 production 签名映射
- 发布输入说明

## 计划

1. 定义版本配置，并让 Gradle、审计和补丁任务共同消费。
2. 保持已发布 APK 观察版本和源码目标版本分离。
3. 定义 QA debug、QA release 和 prod release 的签名输入接口。
4. 在准备 production release 时显式提醒用户验证私钥对应证书 SHA-256 为 `fc30c5efbe58593dbf1432c8dd1c6206006e44effa8c7b76a4303118cbb97bde`；当前按用户决定保持暂缓状态。
5. 在发布 profile 校验 versionCode 单调递增。

## 验收

- applicationId 和版本没有同义常量
- production 签名门禁未通过时不能完成正式发布配置
- production 签名门禁未通过时，报告明确显示用户暂缓决定、baseline 证书摘要和所阻塞的 release 阶段
- 日志、报告和命令摘要不包含私钥或密码
- 已安装 production 用户能够沿原签名更新链升级
- QA 与 production 无法互相覆盖安装

## 文档同步

- 更新版本、签名输入和发布者验证说明

## 完成记录

状态：用户决定暂不管理 production 私钥；执行 production 签名、补丁发布或正式发布前必须重新提醒并完成匹配验证。
