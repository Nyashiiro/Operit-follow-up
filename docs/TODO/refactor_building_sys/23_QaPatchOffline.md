---
title: 步骤 23：差量更新离线场景
status: draft
document_type: implementation-step
track: apk-and-qa
step: 23
depends_on:
  - 20_PatchChain.md
  - 21_QaFixtureFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: compatible_qa_base_and_target_apks
    status: pending_runtime_evidence
    blocks:
      - qa_patch_offline_verification
  - id: qa_debug_device_or_emulator
    status: pending_runtime_evidence
    blocks:
      - qa_patch_offline_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 23：差量更新离线场景

[上一步：插件市场离线场景](./22_QaMarketOffline.md) · [返回总计划](./index.md) · [下一步：公告弹窗离线场景](./24_QaAnnouncementOffline.md)

## 旧实现情况

- 差量更新读取 GitHub Releases，没有可重复的本机端到端场景
- 基础与目标 APK 的来源、签名和版本关系未形成测试输入契约

## 预期的新实现情况

- fixture 提供 Releases 响应、补丁元数据、补丁和目标 APK 信息
- 基础与目标 APK 使用同一 QA 身份和签名，versionCode 严格递增
- 场景复用生产更新检查、下载、重建和安装路径

## 修改作用域

- 更新 fixture 数据
- `qa_patch_offline` Python 场景
- QA debug 更新 endpoint 配置

## 计划

1. 从构建清单取得基础、目标 APK 和补丁输入。
2. 安装基础 APK 并建立 fixture 映射。
3. 触发真实更新检查和补丁下载。
4. 验证基础 hash、补丁 hash、重建 hash 和安装请求。
5. 记录安装后 versionCode 与设备状态。

## 验收

- 重建 APK hash 与目标 APK 一致
- 更新请求不访问正式服务
- QA 与 production 更新链不混用
- 报告能够追溯两个构建清单和补丁元数据
- 场景命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新差量更新 QA 场景说明

## 完成记录

状态：等待步骤 20、21 与两个 QA APK。
