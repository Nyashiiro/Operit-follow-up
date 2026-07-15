---
title: 步骤 21：QA fixture 基础
status: draft
document_type: implementation-step
track: apk-and-qa
step: 21
depends_on:
  - 02_PixiToolchainFoundation.md
  - 03_PipelineStageContracts.md
  - 16_DebugCommandRegistry.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: qa_debug_device_or_emulator
    status: pending_runtime_evidence
    blocks:
      - qa_fixture_device_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 21：QA fixture 基础

[上一步：补丁链](./20_PatchChain.md) · [返回总计划](./index.md) · [下一步：插件市场离线场景](./22_QaMarketOffline.md)

## 旧实现情况

- 在线功能没有共享的本机 fixture、ADB reverse 契约和结构化场景报告
- 各测试若自行实现 server 和设备控制会产生重复状态

## 预期的新实现情况

- Python fixture server 具有固定监听范围、数据目录、请求日志和生命周期
- QA debug endpoint 只能指向明确的本机端口
- 三个场景共享 server 与报告基础，但不共享未声明状态
- 网络约束能够证明测试没有访问正式服务

## 修改作用域

- `ci/tools/qa_fixture/`
- `ci/script/qa/`
- `app/src/debug/` endpoint 配置
- QA report 公共 schema

## 计划

1. 定义 fixture server、端口、数据目录和请求日志。
2. 定义 `adb reverse` 建立与清理流程。
3. 建立 debug-only endpoint 配置和具名场景调用协议。
4. 定义状态清理、超时、诊断日志和报告公共字段。
5. 为测试进程设置明确网络约束，阻止正式 endpoint 请求。

## 验收

- fixture 生命周期和设备映射可重复
- 三个场景可以使用独立数据目录
- 正式服务访问会使场景失败
- qaFullRelease 与 prodFullRelease 不含 fixture endpoint
- fixture 与设备控制命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 QA fixture 与设备准备说明

## 完成记录

状态：等待步骤 16 和测试设备。
