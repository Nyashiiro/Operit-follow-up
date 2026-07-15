---
title: 步骤 29：OAuth Secret 迁移
status: draft
document_type: implementation-step
track: variants-and-security
step: 29
depends_on:
  - 01_BaselineAndUserspaceContracts.md
  - 13_VariantMatrixAndArtifacts.md
  - 14_DebugReleaseBoundary.md
decision_status: proposed
implementation_status: not_started
verification_status: not_run
external_gates:
  - id: oauth_exchange_service_deployed
    status: pending_external
    blocks:
      - oauth_client_cutover
      - oauth_secret_removal
      - prodFullRelease.package_audit_verification
  - id: oauth_provider_configuration_confirmed
    status: pending_external
    blocks:
      - oauth_client_cutover
      - prodFullRelease.package_audit_verification
For_Agent: 未经用户明确授权，不执行编译、构建、测试或发布命令
fork_repository: "git@github.com:Nyashiiro/Operit-follow-up.git"
last_reviewed: 2026-07-15
---

# 步骤 29：OAuth Secret 迁移

[上一步：Debug 与 Release 边界](./14_DebugReleaseBoundary.md) · [返回总计划](./index.md) · [下游：Production Release Policy](./34_ProductionReleasePolicy.md)

## 旧实现情况

- 已发布 APK 内包含不能作为机密保护的 OAuth client secret
- 直接删除 secret 会改变已发布登录协议并可能破坏用户登录
- 客户端、服务端、OAuth provider 和回调配置缺少有顺序的迁移门禁

## 预期的新实现情况

- 可信服务端持有 provider secret，并提供版本化的授权码交换接口
- 新客户端只提交交换协议要求的授权码、PKCE verifier、redirect identity 和防重放字段
- 新客户端不包含、不读取、不下载 OAuth client secret
- 已发布客户端在其支持期内继续使用原有协议；新客户端只有服务端交换这一条协议路径
- 登录入口、账号结果、回调 deep link 和用户可见错误语义保持与已发布用户空间兼容

## 修改作用域

- OAuth exchange 服务协议与部署说明
- exchange 服务的仓库或系统所有者、部署环境、provider 账号所有者和发布责任人
- Android OAuth client、网络配置和 variant 配置
- provider redirect URI 与 client 配置
- 登录兼容与安全验证记录
- APK secret absence 断言输入

## 迁移顺序

1. 记录已发布版本的登录入口、回调 URI、账号绑定行为、错误语义和 provider 配置。
2. 明确 exchange 服务的实现所有者、部署目标、provider 账号所有者和生产变更责任人。
3. 定义服务端交换协议、认证边界、PKCE、state/nonce、防重放、限流、审计日志和敏感字段清理规则。
4. 部署服务端并验证生产与 QA 身份、redirect URI、证书和 provider 配置。
5. 使用独立测试客户端验证授权码只能由服务端交换，secret 不出现在响应、日志或客户端配置中。
6. 修改新 Android 客户端，使其只调用已部署并验证的交换接口。
7. 对照步骤 1 基线验证登录、取消、失败、账号切换、重试和回调恢复行为。
8. 确认服务端部署门禁和客户端验证通过后，删除新 APK 的 secret 声明、资源、BuildConfig 字段、脚本输入和文档示例。
9. 将 secret 名称、已知值 hash、配置 key 和相关字符串加入步骤 34 的 release APK 不存在断言。
10. 单独记录旧客户端支持与 provider 凭据生命周期，不让新客户端重新引入 secret。

## 验收

- 服务端交换接口先于新客户端发布并完成生产配置验证
- 新客户端只使用一个明确的服务端交换协议
- 已发布客户端的支持策略和实际登录行为有验证记录
- 新 APK 的资源、DEX、Manifest、BuildConfig、assets 和网络配置不含 client secret 或其配置 key
- OAuth secret 不出现在 CI 参数、结构化报告、普通日志或错误输出
- 用户可见登录入口、回调和账号结果符合步骤 1 的已发布契约
- 服务端未部署或 provider 配置未确认时，不删除客户端 secret，也不发布使用新协议的客户端

## 文档同步

- 更新 OAuth 架构、服务部署顺序、客户端协议、凭据边界和发布检查说明

## 完成记录

状态：等待服务端交换接口和 provider 配置确认。
