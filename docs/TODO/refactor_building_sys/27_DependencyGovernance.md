---
title: 步骤 27：依赖治理与安全诊断契约
status: draft
document_type: implementation-step
track: build-foundation
step: 27
depends_on:
  - 02_PixiToolchainFoundation.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 27：依赖治理与安全诊断契约

[上一步：Pixi 与宿主工具链基础](./02_PixiToolchainFoundation.md) · [返回总计划](./index.md) · [下一步：流水线阶段契约](./03_PipelineStageContracts.md)

## 旧实现情况

- Python、Conda/Pixi、pnpm、Gradle、外部制品、submodule 和 vendored 源码没有统一登记管理边界
- lockfile 能够复现已有输入，但没有定义主动更新、安全通告、人工前后测试和专用提交的完整流程
- 依赖报告没有统一的数据源状态、输入 hash、有效期和发布判定

## 预期的新实现情况

- 人在依赖变更、完整验证和发布前主动执行 `dependency_update_check`
- CI 提供覆盖全仓的只读诊断与 release 判定
- 发布使用精确 manifest、lockfile、外部制品记录和求解器基线，同时要求匹配且有效的安全诊断报告
- 本步骤定义步骤 32 使用的 registry、报告和更新状态机接口，不实现写入型更新会话

## 修改作用域

- `ci/config/build-system/dependencies/registry.yaml`
- `ci/config/build-system/dependencies/security-policy.yaml`
- `ci/config/build-system/schema/dependency-*.schema.json`
- `ci/script/dependency/`
- `ci/pixi.toml`

## 依赖登记表

registry 每个条目必须声明：

- 稳定 ID、生态和所属工程
- manifest、lockfile 或 solver baseline 的精确路径
- 维护所有者和更新命令
- `managed` 或 `excluded` 状态；`excluded` 必须写明原因、边界、负责人和复审日期
- 直接依赖、传递依赖或外部制品的诊断方式
- 诊断该依赖和选择验证 profile 所需的稳定信息

Git submodule、vendored 第三方源码、代码模板、示例工程和仓库内独立工程必须逐项登记。目录发现只能产生待审清单，不能自动决定管理状态或改写内容。

## 安全通告策略

版本化 policy 必须明确：

- Python、Conda/Pixi、pnpm、Gradle 和外部制品各自必需的 advisory 数据源
- OSV、GitHub Advisory Database、包注册表安全接口、上游供应商公告及其他批准来源的版本或快照信息
- CVE、GHSA、OSV 和供应商编号通过 alias 集合去重，同一受影响包和版本范围只形成一条规范记录
- 使用实际解析版本、生态版本语义和依赖路径匹配受影响范围，不只按包名匹配
- 每个数据源记录查询时间、数据版本、可用状态和响应摘要 hash
- 任一必需数据源不可用时，报告状态为 `incomplete`，不能满足 release 安全门禁
- 报告有效期按 profile 和数据源写入 policy；过期报告不能用于 release
- 当前解析版本命中任何已公布安全漏洞时阻止 release，不因严重性较低而转为普通警告
- 普通版本更新、弃用和兼容性提示只产生维护警告
- 经证据确认不受影响的记录必须保存匹配分析；仍然受影响的记录不能被标记为已解决

## 计划

1. 建立 dependency registry，并逐项审查仓库内所有依赖输入和明确排除项。
2. 建立版本化 security policy、advisory source 清单、去重规则、范围匹配规则和报告有效期。
3. 实现 `dependency_update_check`，覆盖 Python package、Conda/Pixi、pnpm 直接与传递依赖，以及可取得信息的 Gradle 依赖和外部制品。
4. 让检查报告记录全部输入 hash、当前解析版本、候选版本、依赖路径、advisory 证据、数据源状态和 release 判定。
5. 将 `dependency_check` 阶段映射到唯一的 `dependency_update_check` Pixi task，并让所有公开构建入口显示报告状态、更新数量、安全漏洞数量和人工执行提示。
6. 定义步骤 32 所需的 session、checkpoint、人工记录和允许 diff schema，但不在本步骤执行依赖写入或构建测试包。

## 验收

- registry 覆盖所有已知 manifest、lockfile、solver baseline、submodule、vendored 源码、模板和独立工程
- Pixi 可执行程序只声明建议 `>=0.72.2`，仓库不锁用户机器上的 Pixi 版本
- pnpm 建议 `>=11.13`，并随安全和依赖要求持续复审
- `dependency_update_check` 不修改依赖输入
- 任一必需安全数据源不可用、报告过期、输入 hash 不匹配或存在已公布漏洞时阻止 release
- 非安全更新只产生警告和维护记录
- release 使用冻结依赖输入，不在构建中求解新版本
- registry 和报告接口能够被步骤 4、5、31 和 32 扩展或消费，不要求这些后续步骤先完成
- 依赖命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新依赖登记、安全数据源、报告有效期和 release 判定规范

## 完成记录

状态：等待步骤 2 的 Pixi 与共享 Python CLI 基础。
