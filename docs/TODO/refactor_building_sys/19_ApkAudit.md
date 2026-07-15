---
title: 步骤 19：APK 审计
status: draft
document_type: implementation-step
track: apk-and-qa
step: 19
depends_on:
  - 01_BaselineAndUserspaceContracts.md
  - 02_PixiToolchainFoundation.md
  - 03_PipelineStageContracts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 19：APK 审计

[返回总计划](./index.md) · [下一步：Common 与 QA APK 审计策略](./30_CommonAndQaApkAuditPolicies.md)

## 旧实现情况

- APK 缺少统一的身份、签名、Manifest、DEX、assets 和 native 内容审计
- 基线观察结果与构建声明混在同一配置讨论中
- 曾计划自行解析 APK ZIP 和签名块

## 预期的新实现情况

- Python 编排 Android SDK 标准工具生成 APK inventory 和审计报告
- `aapt2` 或 `apkanalyzer` 读取身份与 Manifest
- `apksigner` 验证签名方案与证书
- Python 标准库流式计算 APK、ZIP 条目和内容 hash
- 审计报告是生成产物，不反向成为工具链声明
- 本步骤只建立与变体无关的取证能力，不等待 production 私钥、发布签名验证或 feature 组合完成

## 修改作用域

- `ci/script/apk_audit/`
- APK inventory 与 audit report schema
- `ci/pixi.toml`

## 计划

1. 定义 Python CLI、显式输入和结构化报告。
2. 调用指定 Android SDK 工具读取包身份、SDK、Manifest 和签名。
3. 计算 APK 与条目摘要并拒绝重复或越界 ZIP 路径。
4. 生成 ABI、assets、native 库、DEX、组件和网络配置的通用 inventory，不在本步骤判定具体变体的允许集合。
5. 提供两个 APK inventory 的确定性差异报告。
6. 将通用 inventory 与差异能力暴露给 `package_audit` 阶段。
7. 为依赖更新前后测试 APK 提供身份、Manifest、DEX、assets 和 native 内容差异证据。

## 验收

- 不使用 MJS 或自写 APK 签名块解析器
- 工具路径与版本来自步骤 2
- 报告能够与步骤 1 基线进行确定性比较
- 同一 APK 重复审计得到一致的规范化 inventory
- SDK 工具报告失败、ZIP 路径非法或 APK 结构无法读取时直接失败
- 变体 allowlist、debug/release 内容断言和 production 更新链断言只在步骤 30 定义
- APK 审计命令通过 [CI 开发规范](../../../ci/README.md)一致性检查
- 差异报告只提供证据，升级是否通过由人结合测试结果确认

## 文档同步

- 更新 APK audit CLI、通用 inventory 与差异报告说明；变体规则文档由步骤 30 维护

## 完成记录

状态：baseline APK 已可用于通用取证验证；实现仍等待步骤 1 至 3 的稳定配置与 CLI 基础。
