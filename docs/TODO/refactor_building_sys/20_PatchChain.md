---
title: 步骤 20：补丁链
status: draft
document_type: implementation-step
track: apk-and-qa
step: 20
depends_on:
  - 15_SigningVersionAndUpdateCompatibility.md
  - 19_ApkAudit.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: compatible_base_and_target_apks
    status: pending_runtime_evidence
    blocks:
      - patch_chain_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 20：补丁链

[上一步：APK 审计](./19_ApkAudit.md) · [返回总计划](./index.md) · [下一步：QA fixture 基础](./21_QaFixtureFoundation.md)

## 旧实现情况

- Python hotbuild 工具自行推断旧变体与 APK 文件名
- 补丁元数据没有统一关联构建清单、签名、能力档和输入 manifest
- prod、QA 与不同能力组合的补丁链边界不完整

## 预期的新实现情况

- 复用和迁移现有 Python 补丁算法
- 基础与目标 APK 由构建清单显式提供
- 补丁生成前校验身份、签名、versionCode、能力档、ABI 和基础 hash
- 重建目标 APK 后必须与目标 SHA-256 完全一致
- QA 补丁链可以使用已确认的 QA 签名材料独立推进；production 补丁发布受步骤 15 的私钥匹配门禁阻塞

## 修改作用域

- `tools/hotbuild/` 到 `ci/script/patch/` 的相关迁移
- patch metadata schema
- Pixi patch task

## 计划

1. 记录现有 patch 格式和已发布消费者协议。
2. 将变体和产物路径改为构建清单输入。
3. 增加 identity、signing、version、capability、ABI 与 hash 门禁。
4. 保持已发布补丁格式兼容，格式变化另建版本。
5. 重建目标并核对完整 APK hash。
6. 将 task 映射到 `patch_prepare` 阶段。

## 验收

- 不再推断 Gradle task 或 APK 文件名
- prod、QA 和能力档补丁链互相隔离
- production 私钥验证暂缓时，QA 补丁能力仍可实现和验证，production 补丁发布保持阻塞
- versionCode 严格递增
- 重建结果与目标 APK 完全一致
- 旧消费者需要的协议字段保持可读
- 补丁命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新补丁格式、命令和发布门禁说明

## 完成记录

状态：通用与 QA 补丁实现等待步骤 15 和 19；APK 输入只阻塞验证，production 发布门禁由步骤 34 和 33 管理。
