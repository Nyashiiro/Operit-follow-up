---
title: 步骤 05：外部制品清单
status: draft
document_type: implementation-step
track: foundation
step: 5
depends_on:
  - 01_BaselineAndUserspaceContracts.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: artifact_source_and_owner_confirmation
    status: pending_external
    blocks:
      - artifact_manifest_completion
      - managed_artifact_consumption
  - id: artifact_license_confirmation
    status: pending_external
    blocks:
      - artifact_manifest_completion
      - release_profile
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 05：外部制品清单

[上一步：pnpm workspace 与锁文件](./04_JsWorkspaceAndLockfile.md) · [返回总计划](./index.md) · [下一步：工具职责边界迁移](./06_ToolingBoundaryMigration.md)

## 旧实现情况

- AAR、JAR、JNI、models 和 subpack 由仓库外流程准备
- 当前工作树不能证明来源、版本、许可证、hash、ABI 和唯一消费者
- 源码 native 目标与预编译 native 文件混在同一讨论中
- 用户提供的外层归档已经包含 `libs.zip`、`jniLibs.zip`、`models.zip` 和 `subpack.zip`；文件存在性不再是门禁

## 预期的新实现情况

- 外部归档、解包文件和源码 native 目标分别登记
- 每个文件具有来源、许可证、大小、SHA-256、ABI 和唯一消费者
- 已取得归档的来源、所有者与许可证仍需独立确认

## 修改作用域

- `ci/config/build-system/artifacts/`
- external artifact schema
- 对应许可证记录
- 本步骤文档

## 计划

1. 记录外层归档 `Operit-20260713T191523Z-1-001.zip`：大小 `183399816` bytes，SHA-256 `6fa8679b1845a6143d1394e8863eef3740148b6b7196838917e6637380c3ce19`。
2. 登记其中四类实际归档并确认来源和所有者；外层归档继续位于被忽略的 `.temp/`，不成为构建输入。
3. 计算四个内层归档与解包文件的大小和 SHA-256。
4. 检查 AAR、JAR、Manifest、classes、native ABI 与许可证。
5. 查明同名 native 库的全部提供者和消费者。
6. 单独登记源码 native 目标，不为其伪造归档记录。
7. 实现 Python 制品校验入口，并映射到 `artifact_verify` 阶段。

## 验收

- 每个实际文件都有真实证据
- 目标路径与消费者是一对一关系
- 缺失或未确认制品不会进入完整构建 profile
- 本步骤门禁不阻塞不依赖这些制品的计划轨道
- 制品校验不依赖 Gradle 或 Node.js
- 制品校验命令通过 [CI 开发规范](../../../ci/README.md)一致性检查

## 文档同步

- 更新许可证索引和外部制品准备说明

## 完成记录

状态：四类实际归档已取得；等待来源、所有者、许可证和逐文件 manifest 确认。
