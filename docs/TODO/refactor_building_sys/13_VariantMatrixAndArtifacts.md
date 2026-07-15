---
title: 步骤 13：变体矩阵与产物接口
status: draft
document_type: implementation-step
track: variants-and-security
step: 13
depends_on:
  - 08_GradleToolchainNormalization.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 13：变体矩阵与产物接口

[上一步：Gradle 工具链规范化](./08_GradleToolchainNormalization.md) · [返回总计划](./index.md) · [下一步：Debug 与 Release 边界](./14_DebugReleaseBoundary.md)

## 旧实现情况

- `clone` 与 `nightly` 混合共存安装、调试、签名和更新通道职责
- APK 重命名依赖 AGP 内部输出类型
- prod、QA、full 与 lite 没有独立维度

## 预期的新实现情况

- identity 为 `prod`、`qa`
- build type 为 `debug`、`release`
- 本步骤建立 identity/build type 组合与公开产物接口
- capability 维度和 full/lite 组合由步骤 28 在 feature 隔离后添加
- 产物位置通过公开 Android Components 与 Artifacts API 获取

## 修改作用域

- Gradle flavor 与 variant 配置
- 公开产物定位 build logic

本步骤不配置正式签名，也不移动 debug 代码。

## 计划

1. 记录 identity/build type 组合和未启用组合的理由。
2. 声明 identity 与 build type 维度。
3. 使用 Android Components API 关闭未批准组合。
4. 使用公开 Artifacts API 输出结构化产物位置。

## 验收

- 本步骤产生的变体名可推导 identity 与 build type；步骤 28 完成后再加入 capability
- 不使用 AGP 内部输出类型
- 旧变体尚未删除，删除动作留到步骤 26

## 文档同步

- 更新变体矩阵和产物位置说明

## 完成记录

状态：等待步骤 8，不等待 feature 隔离步骤。
