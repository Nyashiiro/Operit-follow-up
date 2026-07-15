---
title: 构建系统重构计划的本地二进制证据
status: draft
document_type: local-evidence-index
last_reviewed: 2026-07-15
---

# 本地二进制证据

本目录只保存计划审查期间由用户提供并确认的本地二进制证据。二进制本身不提交 Git、不作为构建输入，也不随 TODO 归档。

## 已确认 baseline

| 字段 | 值 |
| --- | --- |
| 本地文件 | `app-release_baseline.apk` |
| 用户确认 | 该 APK 是已发布 baseline |
| 大小 | `398753547` bytes |
| SHA-256 | `ec0db6374e721ea810e4df0c184d2a59b59c1a5c46a879577b07e9a69680bfdb` |
| applicationId | `com.ai.assistance.operit` |
| version | `1.12.0 (44)` |
| ABI | `arm64-v8a` |
| 签名方案 | APK Signature Scheme v2 |
| 证书 SHA-256 | `fc30c5efbe58593dbf1432c8dd1c6206006e44effa8c7b76a4303118cbb97bde` |

身份、版本、Manifest 与证书由 Android SDK 标准工具读取。完整逐条 inventory 属于步骤 19 的生成审计产物；版本控制中的 baseline 只保存规范化摘要、工具版本、生成时间和完整报告 hash。

## 保留规则

- `.gitignore` 精确忽略 APK 原件
- 计划文档和正式 baseline 使用 SHA-256 关联本地证据
- TODO 归档前删除本地 APK，归档内容只保留本说明与稳定 baseline 链接
- production 私钥验证按用户决定保持步骤 15 的 release 专属门禁
