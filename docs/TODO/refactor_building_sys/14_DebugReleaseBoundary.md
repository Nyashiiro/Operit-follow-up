---
title: 步骤 14：Debug 与 Release 边界
status: draft
document_type: implementation-step
track: variants-and-security
step: 14
depends_on:
  - 13_VariantMatrixAndArtifacts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 14：Debug 与 Release 边界

[上一步：变体矩阵与产物接口](./13_VariantMatrixAndArtifacts.md) · [返回总计划](./index.md) · [下一步：签名、版本与更新兼容](./15_SigningVersionAndUpdateCompatibility.md)

## 旧实现情况

- 通用脚本 receiver、ToolPkg 调试入口、action 与实现位于 main source set
- release APK 会包含调试代码和字符串
- build type 的压缩与资源裁剪契约不完整

## 预期的新实现情况

- QA debug source set 拥有外部 ADB 通用执行和调试专属依赖
- 所有 release 不编译通用执行 receiver、QA debug 网关或调试 assets
- debug 不执行 minify；release 执行 R8，并单独声明资源裁剪
- 应用内部 Shell executor 保持既有用户能力

## 修改作用域

- `app/src/debug/`
- `app/src/release/`
- debug/release Manifest、资源、依赖和网络配置
- R8 keep rules 与资源裁剪配置

## 计划

1. 将调试 receiver、实现、actions、assets 与依赖移入 debug source set。
2. 为 release 建立调试类、action、asset 与依赖的不存在断言。
3. 固定 debug 与 release 的压缩行为。
4. 为反射、序列化、JNI 与 ServiceLoader 建立可审计 keep rules。
5. 分开记录资源裁剪设置和内容审计证据。

## 验收

- QA debug 保留完整调试能力
- release Manifest、DEX、assets 和依赖图不含 QA debug 通用执行实现
- release 不包含本机明文 endpoint
- 应用内部 Shell executor 在 full release 中保持可用
- keep rules 与资源裁剪具有独立审计记录

## 文档同步

- 更新 debug/release 能力与网络边界说明

## 完成记录

状态：等待步骤 13。
