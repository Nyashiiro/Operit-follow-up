---
title: 步骤 08：Gradle 工具链规范化
status: draft
document_type: implementation-step
track: foundation
step: 8
depends_on:
  - 02_PixiToolchainFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 08：Gradle 工具链规范化

[上一步：资产准备入口](./07_AssetPreparation.md) · [返回总计划](./index.md) · [下一步：能力架构决策](./09_CapabilityArchitectureDecision.md)

## 旧实现情况

- Wrapper 缺少分发 SHA-256
- compile SDK、Build Tools、NDK、CMake 与 AGP 版本分散或未精确声明
- 环境准备脚本和 Gradle 可能重复声明 Android 组件版本

## 预期的新实现情况

- Gradle 专用版本清单是 compile SDK、Build Tools、NDK、CMake 与 AGP 的唯一事实源
- Python 环境准备只读取同一清单，不维护第二套版本常量
- Wrapper URL 与官方 SHA-256 属于同一发行内容
- 本步骤不等待或修改 AAR、JAR、JNI、models 与 subpack；这些输入由步骤 36 处理

## 修改作用域

- Gradle version catalog 或专用 Android toolchain TOML
- `gradle-wrapper.properties`
- 根与 app 的工具链消费配置
- 必需的 convention build logic

## 计划

1. 建立 Android 编译组件与 AGP 的 Gradle 专用事实源。
2. 让根工程和 Android modules 从同一事实源读取版本。
3. 写入 Wrapper 官方 SHA-256，并核对 URL 对应内容。
4. 让步骤 2 的环境准备读取同一清单并拒绝未声明组件。
5. 静态扫描脚本、Pixi 与 Gradle 中重复的 Android 版本常量。

## 验收

- Android 编译组件版本只有一个声明位置
- Gradle 不安装 SDK、Node 或 Python 依赖
- 环境准备和 Gradle 消费相同的 compile SDK、Build Tools、NDK 与 CMake 版本
- Wrapper URL 与 SHA-256 属于同一发行内容
- identity/build type 变体工作不等待外部制品来源或许可证确认

## 文档同步

- 更新 Android 工具链、Wrapper 与环境准备说明

## 完成记录

状态：等待步骤 2，不等待外部制品步骤。
