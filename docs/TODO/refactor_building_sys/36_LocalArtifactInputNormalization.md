---
title: 步骤 36：本地制品输入规范化
status: draft
document_type: implementation-step
track: foundation
step: 36
depends_on:
  - 05_ExternalArtifactManifest.md
  - 08_GradleToolchainNormalization.md
decision_status: pending_dependency
implementation_status: not_started
verification_status: not_run
external_gates: []
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 36：本地制品输入规范化

[相关步骤：外部制品清单](./05_ExternalArtifactManifest.md) · [返回总计划](./index.md)

## 旧实现情况

- `app/libs` 使用宽泛文件扫描
- native 冲突通过宽泛 `pickFirst` 规则隐藏
- AAR、JAR 与 JNI 路径没有和 external artifact manifest 一一对应

## 预期的新实现情况

- 本地 AAR、JAR 和 JNI 使用精确文件名与唯一 module 所有者
- 重复 native 提供者从输入中删除
- Gradle 只消费已通过步骤 5 登记和校验的本地制品
- 制品 hash、许可证与来源由 `artifact_verify` 检查，Gradle 不复制该算法

## 修改作用域

- `app/build.gradle.kts`
- 相关 feature module Gradle 文件
- 本地 AAR、JAR 与 JNI 依赖声明
- packaging 与 native 冲突配置

## 计划

1. 将本地依赖扫描替换为 external artifact manifest 对应的精确声明。
2. 查明同名 native 库的全部提供者和消费者。
3. 删除重复 native 提供者和宽泛选择规则。
4. 为每个目标路径指定唯一 Android module 所有者。
5. Gradle 对缺失的精确输入直接报错，并引用制品 ID。
6. 静态扫描其他本地目录依赖与宽泛 packaging 规则。

## 验收

- Gradle 本地输入与 external artifact manifest 一一对应
- 每个 AAR、JAR、JNI 和 native 目标只有一个所有者
- `.so` 冲突没有宽泛选择规则
- Gradle 不下载、解包或自行校验业务制品
- 不依赖本地制品的变体与架构步骤不受步骤 5 门禁阻塞

## 文档同步

- 更新本地制品、module 所有权和 Gradle 输入说明

## 完成记录

状态：四类归档已取得，等待步骤 5 完成来源、许可证和 manifest 确认。
