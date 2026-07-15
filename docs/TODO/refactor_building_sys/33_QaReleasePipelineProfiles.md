---
title: 步骤 33：QA 与 Release 流水线 profiles
status: draft
document_type: implementation-step
track: integration-and-docs
step: 33
depends_on:
  - 20_PatchChain.md
  - 22_QaMarketOffline.md
  - 23_QaPatchOffline.md
  - 24_QaAnnouncementOffline.md
  - 27_DependencyGovernance.md
  - 30_CommonAndQaApkAuditPolicies.md
  - 31_BuildPipelineProfiles.md
  - 34_ProductionReleasePolicy.md
  - 35_QaReleaseAndLiteSmoke.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: platform_publish_credentials
    status: pending_external
    blocks:
      - publish
  - id: approved_release_artifacts
    status: pending_runtime_evidence
    blocks:
      - publish
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 33：QA 与 Release 流水线 profiles

[上一步：构建流水线 profiles](./31_BuildPipelineProfiles.md) · [返回总计划](./index.md) · [下一步：文档一致性与旧路径清理](./26_DocumentationAndCleanup.md)

## 旧实现情况

- build、设备 QA、production policy、补丁和发布曾捆绑在一个全局门禁步骤中
- 平台对 QA 子场景、variant 状态、发布授权和产物交接缺少统一表达

## 预期的新实现情况

- `qa` 与 `release` 扩展步骤 31 的 build profile，不重建基础 runner
- QA 报告覆盖三个离线场景、`qaFullRelease` official-test 与 `qaLiteDebug` 行为验证
- release 在冻结构建前要求匹配且有效的依赖安全报告
- publish 凭据和批准产物只阻塞 `publish` stage

## 修改作用域

- pipeline manifest 的 `qa` 与 `release` profile
- release report 与 publish authorization schema
- GitHub Actions 和可视化 CI 的 QA/release 映射

## 计划

1. 让 `qa_verify` 汇总步骤 22 至 24 和步骤 35 的独立状态与证据。
2. 让 `patch_prepare` 消费已通过审计的基础与目标 APK，并输出补丁、重建证据和 metadata。
3. release 启动冻结构建前校验 dependency report 的有效期、policy 版本和全部依赖输入 hash。
4. 当前输入没有匹配且有效的报告时显式运行 `dependency_check`；必需数据源不可用或存在已公布漏洞时阻止后续 release stage，普通更新写入警告。
5. 应用步骤 34 的 `prodFullRelease` policy 和 release 专属门禁。
6. release report 汇总源码、环境、依赖、assets、外部制品、构建、APK、QA、补丁和发布授权的 hash 与状态。
7. `publish` 只在调用方单独授权且凭据、产物门禁满足后启动。
8. 完成 GitHub Actions 与可视化 CI 的 QA/release 状态、权限、日志和产物映射。

## 验收

- QA 场景和 variant 状态分别报告，不被总状态隐藏
- release 不修改 manifest、lockfile、solver baseline 或外部制品记录
- 安全漏洞阻止 release，普通更新只警告
- production 私钥、OAuth 和 production APK 门禁只影响 release 相关 stage
- `publish` 没有单独授权时保持 `not_run`
- 平台凭据按 stage 最小化
- 命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 QA/release profile、variant 状态、依赖门禁、release report 与发布授权说明

## 完成记录

状态：等待 QA 场景、production policy、补丁链和步骤 31 build profile。
