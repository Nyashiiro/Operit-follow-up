---
title: 步骤 01：基线与用户空间契约
status: draft
document_type: implementation-step
track: foundation
step: 1
depends_on: []
decision_status: user_confirmed
implementation_status: partial
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 01：基线与用户空间契约

[返回总计划](./index.md) · [下一步：Pixi 与宿主工具链基础](./02_PixiToolchainFoundation.md)

## 旧实现情况

- 工具版本、应用版本、外部制品和 APK 内容没有清晰区分声明输入与观察结果
- 已发布 applicationId、签名更新链、持久化数据和外部入口缺少集中契约
- 当前工作树已有未提交配置与 APK 记录，来源和决策状态需要逐项核对
- 2026 年 7 月前进入 Git 历史的 JavaScript 文件属于应用、测试、examples 或产品 package，没有通用 Android 构建与 APK 审计 MJS 先例

## 预期的新实现情况

- 建立事实源地图，分别登记声明输入、已发布基线、生成报告和本机状态
- 保存已发布 APK 的身份摘要、来源说明和完整清单 hash
- 用户已确认 `Plan_asserts/app-release_baseline.apk` 是正式 baseline；其 SHA-256、身份、版本与证书摘要构成已确认观察事实
- 建立用户空间契约，覆盖身份、更新、数据、外部入口和用户可见能力
- 未确认值保持未确认，不写入推测值
- 本步骤不决定通用脚本语言，不创建后续领域 schema

## 修改作用域

- `ci/config/build-system/`
- `ci/config/build-system/contracts/`
- `ci/config/build-system/baseline/`
- `ci/config/build-system/schema/`
- `ci/script/build_inventory/`
- 本步骤文档与总计划

本步骤不修改 JavaScript 依赖、Gradle 变体、Android 模块或签名配置。

## 计划

1. 对照 Git 基线和当前工作树，逐项确认已有配置的来源。
2. 定义事实源类别和公共标识、路径、hash 字段。
3. 记录已发布 APK 的 applicationId、版本、证书摘要、ABI 与内容摘要。
4. 将完整逐条 APK inventory 作为审计产物，并在基线摘要中保存其 hash 与生成来源。
5. 建立用户空间契约；私钥验证按用户决定移交步骤 15，只阻塞 production 签名与发布。
6. 删除本步骤中未经确认的 Node/MJS 接口和后续步骤专属 schema。
7. 为当前未提交配置和脚本建立处置表，处理完成前不把任何文件视为已接受实现。

## 当前工作区处置

| 当前内容 | 处置 |
| --- | --- |
| `ci/README.md` | 保留，作为用户确认的 CI 目标规范 |
| `ci/script/build_inventory/` | 删除错误生成的 MJS 实现与配套文档 |
| `ci/config/build-system/schema/` | 只保留步骤 1 已确认的公共与用户空间 schema，后续领域 schema 移到所属步骤 |
| `ci/config/build-system/baseline/published-1.12.0.json` | 提取版本控制摘要；完整逐条 inventory 改为步骤 19 的生成审计产物 |
| `ci/config/build-system/toolchain.json` | 拆分声明输入与观察结果，不让混合记录成为 Gradle 输入 |
| `ci/config/build-system/version.json` | 核对真实来源后决定是否作为步骤 15 的版本事实源 |
| `Plan_asserts/app-release_baseline.apk` | 用户已确认是正式 baseline；以 SHA-256 `ec0db6374e721ea810e4df0c184d2a59b59c1a5c46a879577b07e9a69680bfdb` 记录观察事实，原 APK 仍是计划期证据而非构建输入 |
| `Plan_asserts/README.md` | 保存可提交的 baseline 身份、hash、工具证据和本地 APK 保留规则 |

## 验收

- 每项事实具有唯一类别、来源和所有者
- 已发布基线与当前源码目标分开记录
- baseline 身份固定为 `com.ai.assistance.operit`、`versionCode=44`、`versionName=1.12.0`、`arm64-v8a` 与已观察证书摘要
- 用户空间契约能够约束后续模块、变体和发布改动
- APK 原文件缺失时不宣称完整清单可重建
- 未执行的验证明确标记为 `not_run`
- 错误 MJS 目录和提前创建的后续 schema 已按处置表处理
- baseline APK 被精确忽略，TODO 归档不包含 APK 原件

## 文档同步

- 更新总计划的事实源地图
- 为后续步骤提供用户空间契约链接

## 完成记录

状态：baseline 来源已由用户确认；仍需收敛现有 schema、摘要与观察报告的存放边界。
