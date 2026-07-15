---
title: 步骤 18：Release Support 与数据救援
status: draft
document_type: implementation-step
track: variants-and-security
step: 18
depends_on:
  - 17_ReleaseSupportSecurityDecision.md
decision_status: pending_dependency
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 18：Release Support 与数据救援

[上一步：Release Support 安全决策](./17_ReleaseSupportSecurityDecision.md) · [返回总计划](./index.md)

## 旧实现情况

- Raw snapshot、数据目录定义和恢复 UI 耦合
- 日志读取逻辑分散
- 没有经批准的 release support command 实现

## 预期的新实现情况

- 数据目录和 snapshot 导出服务由 UI 与应用内部 Shell 复用
- 经步骤 17 批准后，release support 只暴露明确登记的只读命令
- 日志输出执行字段级敏感信息清理
- raw snapshot 格式和既有恢复 UI 行为保持不变

## 修改作用域

- 数据救援路径与 snapshot 导出服务
- 日志只读服务
- 经批准的 release support 组件与命令
- UI 和应用内部 Shell 调用方

## 计划

1. 抽出稳定的数据路径 ID 和规范路径解析。
2. 抽出保持现有格式的 snapshot 导出服务。
3. 抽出日志读取、过滤和清理服务。
4. 仅实现步骤 17 批准的命令和调用者验证。
5. 为普通 UID 拒绝、shell/root 成功和输出权限编写验证。

## 验收

- UI 与内部 Shell 复用同一数据救援服务
- 既有 snapshot manifest 与 ZIP 结构不变
- release support 不接受任意路径、SQL、恢复覆盖或普通 shell 命令
- 普通应用 UID 无法调用
- 输出文件能够按安全决策交付给授权调用者

## 文档同步

- 更新数据救援、日志和 release support 指南

## 完成记录

状态：等待步骤 17 批准。
