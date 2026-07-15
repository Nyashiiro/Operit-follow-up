---
title: 步骤 02：Pixi 与宿主工具链基础
status: draft
document_type: implementation-step
track: foundation
step: 2
depends_on:
  - 01_BaselineAndUserspaceContracts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 02：Pixi 与宿主工具链基础

[上一步：基线与用户空间契约](./01_BaselineAndUserspaceContracts.md) · [返回总计划](./index.md) · [下一步：依赖治理与安全诊断契约](./27_DependencyGovernance.md)

## 旧实现情况

- 仓库级 Python 使用 venv，Node.js、pnpm、JDK 和 Android 工具依赖用户环境
- `ci/` 是新目录，尚无统一 workspace、锁文件和公开 task
- Android SDK、NDK、Build Tools 与 CMake 的安装者和消费者边界不明确
- Pixi 是调用方安装的启动工具，仓库不应尝试锁定用户机器上的 Pixi 版本

## 预期的新实现情况

- 建议调用方使用 Pixi `0.72.2` 或更高版本，不把 Pixi 本体写入 workspace 锁定范围
- Pixi 为每次已审查构建锁定 Python、Node.js、pnpm、OpenJDK 与 `android-tools` 的精确求解结果
- pnpm 建议使用 `11.13` 或更高版本，并随依赖要求和安全支持状态更新
- Python 继续作为仓库级工作流语言
- Android SDK 组件版本来自 Gradle 专用版本清单
- Android command-line tools 的发行版本、下载地址和 SHA-256 来自工具链清单，先于 `sdkmanager` 安装
- 环境准备通过 `sdkmanager --sdk_root` 安装明确组件
- `JAVA_HOME`、`ANDROID_HOME` 与 `GRADLE_USER_HOME` 只作用于 task 子进程

## 修改作用域

- 根 Pixi 入口
- `ci/pixi.toml`
- `ci/pixi.lock`
- Android 编译组件版本清单
- `.gitignore`
- `ci/README.md` 的工具链部分

## 计划

1. 将 Pixi `>=0.72.2` 记录为建议的宿主前置条件，不创建 Pixi 本机版本锁。
2. 验证 Pixi workspace 与子 manifest 的真实支持方式，再确定根入口结构。
3. 建立最小 workspace 和锁文件，不提前加入业务任务。
4. 锁定 Python、Node.js、pnpm、OpenJDK 和 ADB/fastboot。
5. 定义 Android command-line tools 的固定发行版本、官方 URL 和 SHA-256，并在仓库本地 Android Home 中安装 `sdkmanager`。
6. 定义 Android SDK、Build Tools、NDK 与 CMake 的唯一版本清单。
7. 定义仓库本地 Android Home 与 Gradle Home，并允许显式重定向。
8. 提供只读 `env-report` 和有明确写入范围的 `android-environment-prepare` task；该 task 先验证 command-line tools，再调用固定的 `sdkmanager --sdk_root`。
9. 为 Python CI 命令建立共享的日志、错误分类、资源检查、交互控制和敏感信息处理基础。

## 验收

- 锁文件能够证明宿主工具精确版本
- 仓库不锁定调用方安装的 Pixi，本机和 CI 文档统一建议 `>=0.72.2`
- pnpm 建议版本为 `>=11.13`，当前发布使用的精确版本由已提交 lockfile 证明
- Node.js 只服务于 JavaScript 产品任务
- Android 组件版本没有在 Pixi、脚本和 Gradle 中重复声明
- 干净环境能够从已校验的 command-line tools 启动 `sdkmanager`，不依赖系统预装 Android SDK
- 环境 task 不写 shell profile、注册表或系统环境变量
- task 的网络、许可证和写入目录在执行前明确报告
- 新命令通过 [CI 开发规范](../../../ci/README.md)一致性检查
- 公共 CLI 基础能够被步骤 27 的依赖治理命令复用

## 文档同步

- 更新 `ci/README.md` 的环境边界和 task 用法
- 更新正式构建文档的宿主工具准备章节

## 完成记录

状态：未开始。
