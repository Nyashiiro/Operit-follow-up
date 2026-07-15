---
title: 步骤 03：流水线阶段契约
status: draft
document_type: implementation-step
track: foundation
step: 3
depends_on:
  - 01_BaselineAndUserspaceContracts.md
  - 02_PixiToolchainFoundation.md
  - 27_DependencyGovernance.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 03：流水线阶段契约

[上一步：依赖治理与安全诊断契约](./27_DependencyGovernance.md) · [返回总计划](./index.md) · [下一步：pnpm workspace 与锁文件](./04_JsWorkspaceAndLockfile.md)

## 旧实现情况

- 计划列出 task 名称，但脚本实现早于统一的输入、输出和阶段报告契约
- 本机、GitHub Actions 与未来可视化 CI 没有共同阶段 ID

## 预期的新实现情况

- pipeline manifest 定义稳定阶段、依赖、输入、输出、权限和报告
- `dependency_check` 是可独立人工调用且受 release 强制约束的阶段
- 每个阶段只引用一个公开 Pixi task
- 本步骤只定义契约和静态校验，不实现平台 runner

## 修改作用域

- `ci/config/build-system/pipeline/`
- `ci/config/build-system/schema/`
- `ci/README.md` 的阶段说明

## 计划

1. 定义 `source_validate`、`dependency_check` 到 `publish` 的稳定阶段 ID。
2. 为阶段定义 `needs`、task、workdir、输入、输出、产物、网络、设备、secret、权限、超时和报告。
3. 定义 `verify`、`build`、`qa` 与 `release` profile。
4. 明确阶段报告格式和失败传播规则。
5. 提供 Python 静态校验入口，检查阶段引用和依赖环。
6. 让 `source_validate` 校验依赖输入，让 `dependency_check` 生成或核对匹配报告，让 `dependency_prepare` 在 release 中要求无安全阻塞项后执行冻结安装。

## 验收

- 后续 task 能映射到唯一阶段
- profile 不根据目录扫描推断阶段
- `publish` 权限不出现在其他阶段
- 平台适配器无需解析脚本内容
- 静态校验命令通过 [CI 开发规范](../../../ci/README.md)一致性检查
- release profile 不在运行过程中更新或重新选择依赖版本
- 普通 build 与 QA 显示诊断状态，不强制网络访问
- release 在没有匹配且有效的报告时必须执行 `dependency_check`
- 安全漏洞状态阻止 release，普通更新状态只产生警告
- 依赖更新维护命令不作为并行流水线阶段，也不自动进入 publish

## 文档同步

- 在总计划和 `ci/README.md` 记录阶段职责

## 完成记录

状态：未开始。
