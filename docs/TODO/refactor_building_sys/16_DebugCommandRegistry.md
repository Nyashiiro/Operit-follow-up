---
title: 步骤 16：QA Debug 命令注册表
status: draft
document_type: implementation-step
track: variants-and-security
step: 16
depends_on:
  - 14_DebugReleaseBoundary.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 16：QA Debug 命令注册表

[上一步：签名、版本与更新兼容](./15_SigningVersionAndUpdateCompatibility.md) · [返回总计划](./index.md) · [下一步：Release Support 安全决策](./17_ReleaseSupportSecurityDecision.md)

## 旧实现情况

- ToolPkg 安装、包刷新和 DSL dump 使用独立 exported receiver
- 通用脚本 receiver 缺少统一的具名命令描述
- 调用脚本需要自行知道 action 和参数细节

## 预期的新实现情况

- QA debug 保留通用执行入口用于探索性调试
- 另有 debug-only 具名命令注册表用于稳定自动化
- 每个命令声明 ID、版本、类型参数、结果、权限、副作用与日志字段
- release 不编译该注册表网关

## 修改作用域

- `app/src/debug/`
- debug command API、registry 与 Manifest
- QA debug 调用脚本
- debug command schema 与生成清单

## 计划

1. 定义 debug command 描述模型和稳定 ID 规则。
2. 创建 debug-only 具名网关和参数校验。
3. 迁移 ToolPkg 安装、包刷新和 DSL dump。
4. 生成机器可读命令清单。
5. 保持通用执行入口与具名入口职责分离。

## 验收

- 未注册 ID、类型错误和权限不匹配明确终止并记录日志
- 具名网关只能执行登记为 QA debug 的命令
- release APK 不含网关、命令实现和 action
- 现有 QA debug 自由调试能力保持可用
- 迁移到 `ci/` 的调用命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新 QA debug 命令注册与调用指南

## 完成记录

状态：等待步骤 14。
