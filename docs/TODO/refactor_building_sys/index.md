---
title: Operit 构建系统重构总计划
status: draft
document_type: refactoring-plan-index
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# Operit 构建系统重构总计划

本目录是工作草稿，不代表已经发布或最终确定的实现。计划以依赖关系组织，不要求所有步骤按文件编号串行完成。每个步骤只交付一个可独立审查的决策或实现单元。

项目已有已发布版本。production applicationId、签名更新链、持久化数据、外部入口和用户可见能力受[基线与用户空间契约](./01_BaselineAndUserspaceContracts.md)约束。

## 目标

- 为宿主工具、JavaScript 依赖、外部制品、Android 构建输入、APK、补丁和流水线阶段建立可审计的唯一事实源
- 本机、外部 CI 和未来可视化 CI 调用同一组 Pixi task
- Gradle 只负责 Android 构建模型，不安装宿主依赖、不下载业务制品、不发布网络资产
- 构建基础设施重构不改变已发布用户空间
- 应用模块化、安全接口和 QA 场景分别审查，不用一条线性依赖隐藏不同风险

## 目录计划

```text
refactor_building_sys/
	01_BaselineAndUserspaceContracts.md
	02_PixiToolchainFoundation.md
	03_PipelineStageContracts.md
	04_JsWorkspaceAndLockfile.md
	05_ExternalArtifactManifest.md
	06_ToolingBoundaryMigration.md
	07_AssetPreparation.md
	08_GradleToolchainNormalization.md
	09_CapabilityArchitectureDecision.md
	10_MediaFeatureIsolation.md
	11_SpeechFeatureIsolation.md
	12_ExportFeatureIsolation.md
	13_VariantMatrixAndArtifacts.md
	14_DebugReleaseBoundary.md
	15_SigningVersionAndUpdateCompatibility.md
	16_DebugCommandRegistry.md
	17_ReleaseSupportSecurityDecision.md
	18_ReleaseSupportAndDataRescue.md
	19_ApkAudit.md
	20_PatchChain.md
	21_QaFixtureFoundation.md
	22_QaMarketOffline.md
	23_QaPatchOffline.md
	24_QaAnnouncementOffline.md
	25_PipelineAdapters.md
	26_DocumentationAndCleanup.md
	27_DependencyGovernance.md
	28_CapabilityVariantComposition.md
	29_OAuthSecretMigration.md
	30_CommonAndQaApkAuditPolicies.md
	31_BuildPipelineProfiles.md
	32_DependencyUpdateWorkflow.md
	33_QaReleasePipelineProfiles.md
	34_ProductionReleasePolicy.md
	35_QaReleaseAndLiteSmoke.md
	36_LocalArtifactInputNormalization.md
	Plan_asserts/
		README.md
		app-release_baseline.apk
	index.md
```

`Plan_asserts/` 只保存当前计划审查所需的本地证据。用户已经确认 APK 是已发布 baseline；APK 原件被精确忽略，不是构建输入，也不随 TODO 归档。版本控制只保存 [证据说明](./Plan_asserts/README.md)、规范化摘要和报告 hash。

## 语言与工具职责

- Gradle Kotlin DSL 管理 Android module、依赖、变体和产物模型
- Python 管理仓库级清单、hash、制品、APK、补丁、QA 与流水线编排；当前使用项目 venv，迁移后由 Pixi 提供
- Node.js、TypeScript 与 pnpm 只用于 Web Chat、MCP bridge、ToolPkg 等 JavaScript 产品及其包内构建
- Batch、Shell 与 PowerShell 只承担平台启动或宿主工具调用，不复制工作流业务逻辑
- APK 身份、Manifest 和签名验证调用 Android SDK 的 `aapt2`、`apkanalyzer`、`apksigner` 等标准工具

Pixi task 是稳定协作接口，task 名称不决定内部实现语言。

### 语言选择证据

本轮以 `git log --before=2026-07-01 --diff-filter=A` 的首次加入记录作为文件进入仓库时间的近似依据。此前与 Android 构建、APK、补丁和发布相关的仓库辅助实现使用 Gradle Kotlin DSL、Python、Shell、Batch 或 PowerShell；已存在的 JavaScript 主要属于应用 assets、Android 端 JavaScript 测试、examples 和各自的产品 package。未发现使用 JavaScript 或 MJS 编排通用 Android 构建、APK 签名解析或仓库级 schema 校验的先例。

因此，新增通用构建能力不以 JavaScript 作为默认实现语言。仓库级清单、APK、补丁、QA 与流水线使用现有 Python 方向，APK 身份和签名读取调用 Android SDK 标准工具；JavaScript 继续留在真实 JavaScript 产品边界内。

Pixi 可执行程序由调用方安装，仓库不锁定用户机器上的 Pixi 版本。当前建议使用 Pixi `0.72.2` 或更高版本；`ci/pixi.lock` 只锁定 workspace 的依赖求解结果。

pnpm 当前建议使用 `11.13` 或更高版本。项目为每次已审查构建提交精确 lockfile，同时持续检查依赖更新与安全通告；依赖变化要求源码适配时，应修改代码并重新验证，不能把可复现构建解释为永久停留在旧依赖。

## CI 命令目标规范

[CI 开发规范](../../../ci/README.md)是用户确认的最终目标之一。它不描述当前仓库已经具备的全部能力，但对所有新增或修改的 `ci/` 命令具有约束力。实现步骤必须最大可能遵循；某项规则确实不适用时，需要在所属模块 README 和完成记录中说明理由。

每个 CI 命令至少审查以下项目：

- 模块 README、复杂命令的 tldr/man 文档和结构化输出 schema
- 交互模式与 `--no-interactive` 的行为边界
- 对可观察变更提供符合规范的 `--dry-run`
- 执行前打印规范化长命令和动作摘要，并隐藏敏感参数
- 耗时操作提供活动状态、进度或心跳
- 文档说明输入、输出、错误、副作用、资源需求、写入位置和耗时来源
- JSON 模式只向 stdout 写 JSON，日志、提示和进度写 stderr
- 使用统一错误分类、明确退出码、超时和 stderr 末行错误对象
- 检查权限、磁盘、内存和并发资源，危险操作使用语义确认
- 由 Pixi 提供依赖和 task，保持 cwd、input、output、缓存和锁文件一致

任何产生或迁移 CI 命令的步骤，都必须在验收记录中附上这份一致性检查结果。

## 唯一事实源

| 信息 | 唯一事实源 | 消费者 |
| --- | --- | --- |
| Pixi 可执行程序 | 调用方宿主环境，建议 `>=0.72.2` | 本机与外部 CI 启动层 |
| Python、Node.js、pnpm、JDK、ADB | `ci/pixi.toml` 与 `ci/pixi.lock` | 本机任务与外部 CI |
| Gradle | `gradle-wrapper.properties` | Gradle Wrapper |
| AGP 与 Android 编译组件版本 | Gradle 专用版本清单 | Gradle 与环境准备脚本 |
| JavaScript 依赖 | package manifest、workspace 与 `pnpm-lock.yaml` | pnpm |
| 应用身份与版本目标 | 独立版本配置 | Gradle、审计与发布任务 |
| 外部二进制与解包文件 | external artifact manifest | 制品校验与 Gradle 精确依赖 |
| JavaScript assets | JS assets manifest | asset 校验与 APK 审计 |
| 流水线阶段和 profile | pipeline manifest | 本机 runner 与平台适配器 |
| 已发布 APK 观察结果 | APK baseline | 兼容检查与差异审计 |

声明输入、已发布基线、构建生成报告和用户机器状态必须分开保存。观察报告不能反向成为构建版本声明。

用户已确认 `Plan_asserts/app-release_baseline.apk` 是正式 baseline。其 SHA-256 为 `ec0db6374e721ea810e4df0c184d2a59b59c1a5c46a879577b07e9a69680bfdb`，applicationId 为 `com.ai.assistance.operit`，版本为 `1.12.0 (44)`，证书 SHA-256 为 `fc30c5efbe58593dbf1432c8dd1c6206006e44effa8c7b76a4303118cbb97bde`。

用户决定暂不管理或验证对应私钥。该决定只在步骤 15 阻塞 production 签名、production 补丁发布和正式发布；不阻塞基线整理、通用 APK 审计、依赖治理、QA、平台适配或其他代码重构。任何 production release 准备都必须显式提醒用户完成证书匹配验证。

## 依赖维护与发布冻结

- 人在准备依赖变更、完整验证或发布前主动执行 `dependency_update_check`
- CI 诊断覆盖 Python package、Conda/Pixi environment、pnpm workspace 的直接与传递依赖，并包含 Gradle 依赖和外部制品能够取得的更新信息
- 安全诊断报告 advisory ID、数据源、严重性、受影响范围、依赖路径和已知修复版本；普通更新另行分类
- 检查任务只输出报告，不修改源码、manifest 或 lockfile；报告记录检查时间、数据源、依赖输入 hash、当前版本、候选版本和审查结论
- 依赖更新作为独立修改提交，必要时同步重写调用代码、迁移配置并更新文档
- 更新后的依赖图通过相应构建、测试和审计后，提交新的精确 lockfile
- 所有公开构建入口在动作摘要中显示报告状态、可更新项、安全漏洞数量和执行提示
- 普通 build 与 QA 展示报告状态和警告，不强制联网检查
- release 准备要求报告与当前依赖输入 hash 匹配且仍在规定有效期内；人未提前生成可用报告时，release runner 显式执行 `dependency_check`
- 已公布安全漏洞阻止 release；非安全版本更新只产生警告并进入维护报告
- 脚本构建依赖更新前后测试包、依赖图和结构化差异；人使用两组测试包执行同一测试集合并负责最终判断
- 确认更新有效后，同一依赖维护工作更新全仓库相关 manifest、lockfile、外部制品记录和求解器基线
- 暂缓更新项必须记录原因、影响、负责人和期限；超过维护期限或当前解析版本命中任何已公布安全漏洞的依赖不能进入发布输入
- release profile 使用冻结安装，禁止在构建过程中选择新版本
- 构建清单记录各 lockfile、Gradle 依赖声明、外部制品 manifest 和依赖诊断报告的 hash，release 缺少匹配报告时不能进入冻结构建

锁定用于复现已审查输入；持续更新用于维护兼容性和安全性，两者都属于发布流的必需环节。

步骤 27 只建立 registry、安全策略、只读诊断和 release 判定。写入型依赖更新由[步骤 32](./32_DependencyUpdateWorkflow.md)采用串行、可恢复的人工主导流程：

1. `dependency_update prepare` 记录旧基线并构建升级前测试包。
2. 人执行升级前测试并保存结果。
3. `dependency_update apply` 串行更新能够自动处理的依赖与求解基线。
4. 命令自身依赖需要升级时，脚本写入 bootstrap 检查点，输出人工升级命令和 `dependency_update resume`。
5. `dependency_update resume` 继续处理并构建升级后测试包与差异报告。
6. 人执行同一测试集合并确认差异。
7. `dependency_update finalize` 在新依赖输入上重新诊断漏洞，校验人工测试记录、diff 范围和全仓求解基线。
8. 依赖输入、必要适配代码、测试与文档形成一个专用 commit，不混入无关修改。

同一工作区不并行运行依赖更新会话。依赖更新 commit 可以属于正常发布提交序列，不要求单独发布。

全仓依赖范围由 dependency registry 明确列出。每个条目声明生态、manifest、lockfile、所有者、更新命令和管理状态；Git submodule、vendored 源码、模板与独立工程必须逐项决定，不能由目录扫描直接改写。

## 依赖轨道

### 构建基础

- 步骤 01：[基线与用户空间契约](./01_BaselineAndUserspaceContracts.md)
- 步骤 02：[Pixi 与宿主工具链基础](./02_PixiToolchainFoundation.md)
- 步骤 27：[依赖治理与安全诊断契约](./27_DependencyGovernance.md)
- 步骤 03：[流水线阶段契约](./03_PipelineStageContracts.md)
- 步骤 04：[pnpm workspace 与锁文件](./04_JsWorkspaceAndLockfile.md)
- 步骤 05：[外部制品清单](./05_ExternalArtifactManifest.md)
- 步骤 06：[工具职责边界迁移](./06_ToolingBoundaryMigration.md)
- 步骤 07：[资产准备入口](./07_AssetPreparation.md)
- 步骤 08：[Gradle 工具链规范化](./08_GradleToolchainNormalization.md)
- 步骤 36：[本地制品输入规范化](./36_LocalArtifactInputNormalization.md)

pnpm workspace 与外部制品清单可以并行。外部制品的来源或许可证门禁只阻塞 artifact manifest 完成、受管理制品消费和 release，不阻塞 Pixi、依赖诊断、JS workspace、Gradle 工具链、identity/build type 变体或流水线契约。

### Android 架构

- 步骤 09：[能力架构决策](./09_CapabilityArchitectureDecision.md)
- 步骤 10：[媒体 feature 隔离](./10_MediaFeatureIsolation.md)
- 步骤 11：[语音 feature 隔离](./11_SpeechFeatureIsolation.md)
- 步骤 12：[导出 feature 隔离](./12_ExportFeatureIsolation.md)
- 步骤 28：[Capability 变体组合](./28_CapabilityVariantComposition.md)

三个 feature 步骤相互独立，并共同依赖步骤 9。每一步都以步骤 1 的用户空间契约为行为比较基线。

### 变体与安全边界

- 步骤 13：[变体矩阵与产物接口](./13_VariantMatrixAndArtifacts.md)
- 步骤 14：[Debug 与 Release 边界](./14_DebugReleaseBoundary.md)
- 步骤 15：[签名、版本与更新兼容](./15_SigningVersionAndUpdateCompatibility.md)
- 步骤 16：[QA Debug 命令注册表](./16_DebugCommandRegistry.md)
- 步骤 17：[Release Support 安全决策](./17_ReleaseSupportSecurityDecision.md)
- 步骤 18：[Release Support 与数据救援](./18_ReleaseSupportAndDataRescue.md)
- 步骤 29：[OAuth Secret 迁移](./29_OAuthSecretMigration.md)
- 步骤 34：[Production Release Policy](./34_ProductionReleasePolicy.md)

步骤 17 是显式安全决策门禁。用户不批准新增 release support 接口时，记录决策并将步骤 18 标记为 `not_applicable`；该条件轨道不影响构建基础轨道完成。

### APK、补丁与 QA

- 步骤 19：[APK 审计](./19_ApkAudit.md)
- 步骤 30：[Common 与 QA APK 审计策略](./30_CommonAndQaApkAuditPolicies.md)
- 步骤 20：[补丁链](./20_PatchChain.md)
- 步骤 21：[QA fixture 基础](./21_QaFixtureFoundation.md)
- 步骤 22：[插件市场离线场景](./22_QaMarketOffline.md)
- 步骤 23：[差量更新离线场景](./23_QaPatchOffline.md)
- 步骤 24：[公告弹窗离线场景](./24_QaAnnouncementOffline.md)
- 步骤 35：[QA Release 与 Lite 运行验证](./35_QaReleaseAndLiteSmoke.md)

### 平台与文档

- 步骤 25：[流水线平台适配](./25_PipelineAdapters.md)
- 步骤 31：[构建流水线 profiles](./31_BuildPipelineProfiles.md)
- 步骤 32：[依赖更新工作流](./32_DependencyUpdateWorkflow.md)
- 步骤 33：[QA 与 Release 流水线 profiles](./33_QaReleasePipelineProfiles.md)
- 步骤 26：[文档一致性与旧路径清理](./26_DocumentationAndCleanup.md)

步骤 31 提前交付不依赖 OAuth、production 私钥、设备或发布凭据的 verify/build。步骤 33 在其上增加 QA、production policy、补丁和 publish。正式文档随每个步骤同步；步骤 26 只执行全局一致性检查、旧入口清理和归档准备。

## 稳定流水线阶段

阶段契约由步骤 3 定义，具体 task 在所属步骤实现：

1. `source_validate`
2. `dependency_check`
3. `environment_prepare`
4. `dependency_prepare`
5. `asset_prepare`
6. `artifact_verify`
7. `static_verify`
8. `test_execute`
9. `android_assemble`
10. `package_audit`
11. `qa_verify`
12. `patch_prepare`
13. `publish`

每个阶段只调用一个公开 Pixi task。`publish` 需要调用方明确授权，其他阶段不继承发布权限。

`dependency_check` 是流水线阶段 ID，并映射到供人主动调用的唯一 `dependency_update_check` Pixi task，不维护两套诊断实现。

## 状态与完成证据

每个步骤分别记录：

- `decision_status`：方案是否经过确认
- `implementation_status`：代码和文档是否落地
- `verification_status`：计划要求的验证是否完成
- `external_gates`：签名材料、外部归档、设备或用户决策等具有明确作用域的门禁

状态字段使用固定枚举：

- `decision_status`：`proposed`、`user_confirmed`、`pending_dependency`、`requires_user_approval`、`not_applicable`
- `implementation_status`：`not_started`、`partial`、`complete`、`not_applicable`
- `verification_status`：`not_run`、`passed`、`failed`、`not_applicable`

非空 `external_gates` 使用统一结构：

- `id`：稳定门禁 ID
- `status`：`pending_external`、`requires_user_decision`、`deferred_by_user` 或 `pending_runtime_evidence`
- `blocks`：被阻塞的 step、profile、variant、stage 或完成动作 ID；未列出的工作不受影响
- `reminder`：需要再次提醒用户的明确时点，可省略

`depends_on` 只表达实现前置条件，不继承上游门禁的全部阻塞范围。阻塞原因只写入 `external_gates` 和完成记录，不写入状态字段。运行时会话需要的 APK、设备、人工测试记录和发布授权应作为对应 verification 或 stage 的输入门禁，不能阻塞工具及其他 profile 的实现。

Agent 未获授权时不执行编译、构建、测试或发布。实现可以完成，但验证状态必须保持未执行，不能用静态阅读代替运行证据。

每步完成后同步后续计划和正式文档，并在一级标题末尾添加 `[DONE]`。所有必需轨道完成且用户明确确认后，才按 TODO 规范归档本目录。
