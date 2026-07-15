---
title: 步骤 28：Capability 变体组合
status: draft
document_type: implementation-step
track: android-architecture
step: 28
depends_on:
  - 10_MediaFeatureIsolation.md
  - 11_SpeechFeatureIsolation.md
  - 12_ExportFeatureIsolation.md
  - 13_VariantMatrixAndArtifacts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 28：Capability 变体组合

[上一步：导出 feature 隔离](./12_ExportFeatureIsolation.md) · [相关步骤：变体矩阵与产物接口](./13_VariantMatrixAndArtifacts.md) · [返回总计划](./index.md)

## 旧实现情况

- full 与 lite 不是独立的 capability 维度
- feature 实现和外部制品可能由 `:app` 无条件声明，lite 仍会解析或打包完整能力依赖
- 身份、build type 与能力组合的批准集合未集中定义

## 预期的新实现情况

- 在步骤 13 的 identity 与 build type 之上增加 `capability=full|lite`
- 只批准 `qaFullDebug`、`qaLiteDebug`、`qaFullRelease` 和 `prodFullRelease`
- full 变体声明媒体、语音与导出 feature 实现及其制品
- lite 变体不声明、不解析、不打包这些 feature 实现和专属制品
- capability API 保持稳定，应用壳通过构建期组合取得明确实现

## 修改作用域

- `app/build.gradle.kts`
- Android variant 与依赖声明
- capability 组合配置
- 构建清单与产物映射

## 计划

1. 增加 capability flavor dimension，并集中定义批准组合。
2. 在 variant 配置阶段禁用未批准组合，使其不产生 task 或公开产物。
3. 将三个 feature module 和专属外部制品只关联到 full 配置。
4. 为 lite 提供明确的产品行为和 UI 能力边界，不引用 full 实现类或资源。
5. 校验 lite 的解析依赖图、打包资源、native 库和 assets 不含 full feature 内容。
6. 让构建清单记录 identity、capability、build type 和对应依赖输入 hash。
7. 维持步骤 13 的稳定产物查询接口，调用方不得推断 task 名或 APK 文件名。

## 验收

- 仅四个批准变体可构建和查询产物
- `qaFullDebug`、`qaFullRelease` 与 `prodFullRelease` 拥有完整 feature 依赖
- `qaLiteDebug` 不解析或打包媒体、语音、导出实现及其专属制品
- lite 不以运行时开关隐藏已经打包的 full 内容
- capability API 的用户可见契约符合步骤 1 基线
- 构建清单能够区分 identity、capability 和 build type

## 文档同步

- 更新变体矩阵、feature 所有权、lite 行为边界和产物查询说明

## 完成记录

状态：等待三个 feature 隔离步骤和步骤 13。
