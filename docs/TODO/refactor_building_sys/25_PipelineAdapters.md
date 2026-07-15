---
title: 步骤 25：流水线平台适配
status: draft
document_type: implementation-step
track: integration-and-docs
step: 25
depends_on:
  - 03_PipelineStageContracts.md
  - 07_AssetPreparation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 25：流水线平台适配

[返回总计划](./index.md) · [下一步：构建流水线 profiles](./31_BuildPipelineProfiles.md)

## 旧实现情况

- 本机、GitHub Actions 与未来可视化 CI 没有共享的最小 runner
- 平台接入容易先复制环境、依赖与资产准备命令，后续再复制构建和发布命令

## 预期的新实现情况

- Python 本机 runner 读取步骤 3 的 pipeline manifest，先支持 `source_validate`、`dependency_check`、`environment_prepare`、`dependency_prepare` 与 `asset_prepare`
- GitHub Actions job 只准备 runner、调用上述阶段 ID 和上传声明产物
- 可视化 CI 可读取相同阶段、依赖、状态、日志和产物接口
- 平台适配层不包含业务构建逻辑
- Android 构建、APK 审计、QA、补丁、发布和 release 门禁由步骤 31 在相同接口上补齐

## 修改作用域

- `ci/script/pipeline/`
- `.github/workflows/`
- 可视化 CI 读取接口
- `ci/README.md`

## 计划

1. 实现按 profile 执行阶段的 Python runner。
2. 校验阶段输入 hash 和上游报告再启动 task。
3. 建立只覆盖基础阶段的精简 GitHub Actions job 映射。
4. 定义可视化 CI 的 manifest 读取、状态回传、日志和产物接口。
5. 验证基础 profile 不请求设备、签名或发布权限。
6. 所有基础构建入口显示依赖诊断状态、可更新数量、安全漏洞数量和人工检查命令。
7. 为步骤 31 预留通过 manifest 扩展阶段和 profile 的稳定接口，不预置未实现阶段的成功状态。

## 验收

- 三种调用面使用同一阶段 manifest 和 Pixi task
- workflow 不复制业务命令
- 失败阶段阻止依赖阶段启动
- runner 与平台调用命令通过 [CI 开发规范](../../../ci/README.md)一致性检查
- runner 不修改 manifest 或 lockfile
- 基础 profile 不要求 APK、设备、签名材料或发布凭据
- 平台 runner 不并行启动依赖更新维护会话，依赖更新 commit 不被强制拆成独立发布节点

## 文档同步

- 更新平台映射、权限和产物交接说明

## 完成记录

状态：等待步骤 3 和 7 的基础阶段接口。
