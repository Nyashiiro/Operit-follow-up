---
title: 步骤 31：构建流水线 profiles
status: draft
document_type: implementation-step
track: integration-and-docs
step: 31
depends_on:
  - 05_ExternalArtifactManifest.md
  - 07_AssetPreparation.md
  - 13_VariantMatrixAndArtifacts.md
  - 19_ApkAudit.md
  - 25_PipelineAdapters.md
  - 28_CapabilityVariantComposition.md
  - 36_LocalArtifactInputNormalization.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 31：构建流水线 profiles

[上一步：流水线平台适配](./25_PipelineAdapters.md) · [返回总计划](./index.md) · [下游：QA 与 Release 流水线](./33_QaReleasePipelineProfiles.md)

## 旧实现情况

- 步骤 25 只建立基础 runner 与平台接口
- Android build 被推迟到 QA、补丁、OAuth 和发布步骤全部完成之后
- `artifact_verify` 虽有阶段和 task 计划，但没有接入完整 build profile

## 预期的新实现情况

- `verify` 与 `build` profile 在 QA、OAuth、production 私钥和发布凭据之前可用
- 本机、GitHub Actions 与未来可视化 CI 使用同一 pipeline manifest 和 Pixi task
- build profile 显式执行 `artifact_verify`、Android 构建与通用 APK 审计
- 本步骤不包含设备 QA、补丁发布或 publish

## 修改作用域

- pipeline manifest 的 `verify` 与 `build` profile
- `ci/script/pipeline/`
- `.github/workflows/` 的 verify/build jobs
- 可视化 CI build profile 接口

## 计划

1. 在步骤 25 的 runner 上启用 `artifact_verify`、`static_verify`、`test_execute`、`android_assemble` 和通用 `package_audit`。
2. 为 `verify` 与 `build` 明确阶段顺序、输入、权限、缓存和声明产物。
3. 让 `android_assemble` 只接收批准变体 ID，并通过构建清单取得产物。
4. 让通用 `package_audit` 绑定 APK hash、构建清单和步骤 19 inventory，不提前应用 production release policy。
5. 完成 GitHub Actions 和可视化 CI 的 verify/build 映射，不复制业务命令。
6. 所有入口显示依赖报告状态、可更新数量、安全漏洞数量和人工检查提示。

## 验收

- build profile 包含 `source_validate`、`dependency_check`、`environment_prepare`、`dependency_prepare`、`asset_prepare`、`artifact_verify`、`static_verify`、`test_execute`、`android_assemble` 和通用 `package_audit`
- 外部制品未通过 `artifact_verify` 时不启动依赖它的 Android 构建
- Android build 不等待 OAuth、production 私钥、设备、补丁或发布凭据
- profile 实现和非 production 变体不受 production 门禁影响；选择 `prodFullRelease` 时只将受门禁的 stage 标记为 `not_run`
- workflow 和可视化配置不复制 Gradle、pnpm、APK 或制品业务命令
- runner、profile 与平台命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 verify/build profile、阶段顺序、产物和平台映射

## 完成记录

状态：等待最终变体组合、外部制品、通用 APK 审计和步骤 25 runner。
