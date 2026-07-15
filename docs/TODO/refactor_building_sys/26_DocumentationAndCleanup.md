---
title: 步骤 26：文档一致性与旧路径清理
status: draft
document_type: implementation-step
track: integration-and-docs
step: 26
depends_on:
  - 32_DependencyUpdateWorkflow.md
  - 33_QaReleasePipelineProfiles.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: user_confirms_todo_archive
    status: requires_user_decision
    blocks:
      - todo_archive
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 26：文档一致性与旧路径清理

[上一步：QA 与 Release 流水线 profiles](./33_QaReleasePipelineProfiles.md) · [返回总计划](./index.md)

## 旧实现情况

- 构建文档仍可能引用 npm、全局工具、旧变体、旧路径和旧 APK 文件名
- TODO、脚本 `--help`、Pixi task 与正式文档可能发生偏差

## 预期的新实现情况

- 正式构建文档只描述当前 Pixi、pnpm、制品、变体、审计和发布入口
- CI README、工具 README 和脚本帮助相互一致
- 旧 npm 混用入口、旧 Gradle 变体、旧路径和过期说明被删除
- TODO 保留实际 PR、验证记录与稳定接口链接

## 修改作用域

- `docs/doc-src/dev-core/BUILDING.md`
- `ci/README.md`
- 各工具与 package README
- `Repo_Arch_Basic.md`
- 本 TODO 目录

本步骤不补写前序步骤遗漏的实现文档；每一步必须在完成时同步自身文档。

## 计划

1. 从 Pixi task、pipeline manifest 和各 CLI `--help` 核对任务清单。
2. 核对环境、assets、制品、变体、签名、审计、QA 和发布说明。
3. 删除旧 npm 命令、旧 build type、旧任务别名和旧 APK 路径。
4. 核对工具目录职责和新增 package/tool 指南。
5. 汇总所有 CI 命令对 [CI 开发规范](../../../ci/README.md)的一致性检查和不适用说明。
6. 记录 pnpm `>=11.13` 建议、人工主动诊断、Python/Conda/pnpm 全覆盖、构建入口提示、release 强制检查、安全阻塞、普通更新警告、源码适配、冻结安装和发布输入 hash 的完整流程。
7. 记录人工测试责任、更新前后测试包、串行会话、bootstrap 检查点、继续命令和结构化差异流程。
8. 记录依赖专用 commit 的允许内容、禁止混入项，以及它与正常发布提交序列的关系。
9. 记录全仓 manifest/lockfile/求解基线更新和暂缓项治理流程。
10. 为每一步补充实际 PR、验证状态和稳定接口链接。
11. 用户确认后按 TODO 规范单独归档本目录。
12. 归档前确认 `Plan_asserts/app-release_baseline.apk` 未被 Git 跟踪且不进入归档提交。

## 验收

- 新协作者只阅读 BUILDING 与 CI README 就能定位当前入口
- 文档中的 task、参数、权限和产物路径与实现一致
- 正式文档不再引用已删除入口
- 所有已完成步骤具有对应文档和验证证据
- 未经用户确认不执行归档

## 文档同步

- 本步骤本身完成正式文档全局一致性检查

## 完成记录

状态：等待步骤 32、33 和其他必需轨道完成；用户确认只阻塞 TODO 归档动作。
