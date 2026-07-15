---
title: 步骤 17：Release Support 安全决策
status: draft
document_type: security-decision-step
track: variants-and-security
step: 17
depends_on:
  - 01_BaselineAndUserspaceContracts.md
  - 14_DebugReleaseBoundary.md
  - 16_DebugCommandRegistry.md
decision_status: requires_user_approval
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: user_approves_published_release_support_interface
    status: requires_user_decision
    blocks:
      - step_18
  - id: android_caller_identity_experiment
    status: pending_runtime_evidence
    blocks:
      - release_support_security_decision
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 17：Release Support 安全决策

[上一步：QA Debug 命令注册表](./16_DebugCommandRegistry.md) · [返回总计划](./index.md) · [下一步：Release Support 与数据救援](./18_ReleaseSupportAndDataRescue.md)

## 旧实现情况

- 已发布版本没有统一的外部 support command 网关
- 数据救援主要通过应用 UI 完成
- 直接新增 release exported 组件会扩大已发布外部接口和攻击面

## 预期的新实现情况

- 先完成安全决策、调用者身份实验和数据流设计，再决定是否实现
- 明确 ADB shell、root、普通应用 UID 与应用内部调用的权限边界
- release 不提供任意命令、任意路径、任意 SQL 或恢复覆盖
- 该决策不阻塞构建基础、模块化和 QA debug 轨道

## 修改作用域

- 安全 ADR
- 威胁模型
- Android 调用者身份实验源码与记录
- support command API 草案

本步骤不向 release Manifest 添加组件。

## 计划

1. 列出资产、攻击者、入口、敏感数据和滥用场景。
2. 验证 Broadcast、Service 或 Binder 方案能否可靠识别 shell/root 调用者。
3. 设计导出文件交付方式，证明 shell 能取得授权输出而不能读取其他私有数据。
4. 定义日志清理、读取上限、超时和审计记录。
5. 由用户确认该已发布版本是否接受新增 support 接口。

## 验收

- 身份验证基于真实平台实验，不依赖 action 名称
- 普通应用 UID 无法取得 support 数据
- 数据导出路径、权限和生命周期明确
- 用户未批准时步骤 18 保持未开始
- 用户明确不批准时，步骤 18 标记为 `not_applicable`，其他轨道继续执行

## 文档同步

- 形成独立安全决策文档和实验记录

## 完成记录

状态：等待用户批准与平台实验。
