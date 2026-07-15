---
title: 步骤 24：公告弹窗离线场景
status: draft
document_type: implementation-step
track: apk-and-qa
step: 24
depends_on:
  - 21_QaFixtureFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: qa_full_debug_apk
    status: pending_runtime_evidence
    blocks:
      - qa_announcement_offline_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 24：公告弹窗离线场景

[上一步：差量更新离线场景](./23_QaPatchOffline.md) · [返回总计划](./index.md) · [下一步：流水线平台适配](./25_PipelineAdapters.md)

## 旧实现情况

- 远程公告 pointer、payload、筛选、弹窗和已读状态没有离线端到端场景
- 旧计划曾将应用内公告与系统通知监听混为同一目标

## 预期的新实现情况

- fixture 提供生产模型一致的 pointer 与 payload
- 场景验证筛选、弹窗内容、倒计时和已读持久化
- 明确不测试 `NotificationListenerService`

## 修改作用域

- 公告 fixture 数据
- `qa_announcement_offline` Python 场景
- QA debug 公告 endpoint 配置

## 计划

1. 建立 pointer、payload 和边界条件 fixture。
2. 清理场景专属公告已读状态。
3. 触发真实公告拉取与 UI 展示。
4. 验证版本、渠道、语言、时间、启用状态和倒计时筛选。
5. 验证确认后的已读持久化并输出报告。

## 验收

- 公告请求只到达 fixture
- UI 内容和持久化结果与 fixture 一致
- 报告明确标识应用内远程公告
- 系统通知监听结果不参与通过条件
- 场景命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新公告 QA 场景说明

## 完成记录

状态：等待步骤 21。
