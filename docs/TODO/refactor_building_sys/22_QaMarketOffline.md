---
title: 步骤 22：插件市场离线场景
status: draft
document_type: implementation-step
track: apk-and-qa
step: 22
depends_on:
  - 21_QaFixtureFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: qa_full_debug_apk
    status: pending_runtime_evidence
    blocks:
      - qa_market_offline_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 22：插件市场离线场景

[上一步：QA fixture 基础](./21_QaFixtureFoundation.md) · [返回总计划](./index.md) · [下一步：差量更新离线场景](./23_QaPatchOffline.md)

## 旧实现情况

- 插件市场直接访问正式 API 与静态资源
- 没有固定的离线列表、详情、搜索、下载和安装场景

## 预期的新实现情况

- fixture 提供与生产模型一致的市场 API 和资源响应
- 场景复用生产 HTTP、解析、缓存、下载和安装实现
- 报告记录请求、状态变化、安装结果和诊断日志

## 修改作用域

- 市场 fixture 数据
- `qa_market_offline` Python 场景
- QA debug 市场 endpoint 配置

## 计划

1. 建立列表、分类、详情、搜索、排序与资源 fixture。
2. 注册具名 QA debug 场景命令。
3. 清理场景专属市场状态并触发真实刷新。
4. 验证解析、下载、安装和本地状态。
5. 输出独立结构化报告。

## 验收

- 所有市场请求到达 fixture
- 场景不建立第二套市场业务实现
- 安装结果和本地状态与 fixture 一致
- 单项失败不会被总状态隐藏
- 场景命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新市场 QA 场景说明

## 完成记录

状态：等待步骤 21。
