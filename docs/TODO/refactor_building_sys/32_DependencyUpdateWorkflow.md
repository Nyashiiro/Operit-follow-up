---
title: 步骤 32：依赖更新工作流
status: draft
document_type: implementation-step
track: build-foundation
step: 32
depends_on:
  - 04_JsWorkspaceAndLockfile.md
  - 05_ExternalArtifactManifest.md
  - 07_AssetPreparation.md
  - 19_ApkAudit.md
  - 27_DependencyGovernance.md
  - 31_BuildPipelineProfiles.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 32：依赖更新工作流

[上一步：构建流水线 profiles](./31_BuildPipelineProfiles.md) · [返回总计划](./index.md)

## 旧实现情况

- 依赖治理契约与写入型更新会话曾混在步骤 27，形成后续生态步骤反向依赖
- 更新命令自身依赖变化时缺少可审计的 bootstrap 检查点
- 人工前后测试、测试包和依赖专用 commit 缺少会话级状态

## 预期的新实现情况

- 复用步骤 27 的 registry、安全诊断和报告 schema
- `prepare`、`apply`、`resume`、`finalize` 严格串行
- 脚本构建更新前后测试包、依赖图和结构化差异，人负责使用同一测试集合判断行为
- 每次依赖维护形成专用 commit，但不强制形成独立发布节点

## 修改作用域

- `ci/script/dependency/` 的写入型子命令
- `ci/.state/dependency-update/<session-id>/`
- 人工测试记录、checkpoint 与 diff schema
- `ci/pixi.toml`
- `.gitignore`

## 更新会话

- 状态目录被 Git 忽略并记录仓库根路径、分支、起始 commit、依赖输入 hash、阶段、写入集合和人工记录引用
- `prepare` 只接受专用于依赖维护的干净 worktree
- 同一 worktree 同时只能存在一个活动更新会话
- 每个写入阶段开始和结束都原子更新检查点
- 人工测试记录是具体 session 的 `finalize` 输入，不是实现本工具的前置门禁

## 计划

1. 实现 `dependency_update prepare`：校验干净 worktree，保存旧依赖图和输入 hash，并在明确调用时使用步骤 31 构建升级前测试包。
2. 校验升级前人工记录包含测试集合版本、测试包 hash、设备或环境、操作者、时间和结论。
3. 实现 `dependency_update apply`：按 registry 顺序串行更新自动管理项、lockfile 和 solver baseline，不并行修改多个生态。
4. 命令运行环境发生变化时写入 bootstrap 检查点，打印人工命令和唯一的 `dependency_update resume` 启动命令并结束本阶段。
5. 实现 `dependency_update resume`：核对检查点与 worktree，完成剩余更新并构建升级后测试包、依赖图和结构化差异。
6. 校验升级后人工记录使用同一测试集合，并关联新测试包 hash、环境、操作者、时间和结论。
7. 实现 `dependency_update finalize`：在新输入 hash 上重新执行步骤 27 的完整安全诊断，拒绝复用旧 hash 报告。
8. `finalize` 校验人工记录、允许 diff 范围、全部 registry 条目、manifest、lockfile、外部制品记录和 solver baseline。
9. 输出依赖专用 commit 的拟提交文件清单；适配新依赖所需的代码、测试和文档必须关联对应 registry 条目，无关修改使 `finalize` 失败。

## 验收

- 四个子命令严格串行且检查点可审计
- bootstrap 后只从明确检查点继续
- 更新前后测试包来自同一 build profile 与测试集合
- `finalize` 在新 hash 上重新诊断并验证人工前后测试记录
- commit 只含依赖输入、必要适配代码、对应测试和文档
- 同一工作区不能并行运行更新会话
- 命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新人工责任、状态机、bootstrap、测试记录、差异报告和专用 commit 指南

## 完成记录

状态：等待依赖生态接口、APK 差异审计和步骤 31 的 build profile。
