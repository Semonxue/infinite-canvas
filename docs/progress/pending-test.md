---
title: 待测试
description: 当前版本已实现但仍需人工验证的变更项
---

# 待测试

## 参考素材本地地址识别修复

`web/src/services/api/direct-ai.ts` 的 `publicReferenceURL` 用于判断参考素材地址能否直接透传给上游，原先只识别 `localhost`、`127.0.0.1`、`[::1]`。现在补全为：

| 类型 | 范围 |
| --- | --- |
| mDNS | `*.local` |
| 私有 IPv4 | `10.x`、`127.x`、`172.16-31.x`、`192.168.x`、`169.254.x` |
| 私有 IPv6 | `fc`、`fd`、`fe80` 前缀 |

IPv4 按四段整数逐段判断，IPv6 先去掉 `URL.hostname` 保留的方括号，避免把 `10.example.com`、`fdic.gov` 这类普通域名误判为本地。

`web/src/services/api/video.ts` 的视频请求组装中，图片 references 与首尾帧统一改用 `imageToDataUrl` 转 data URL，与其余视频渠道保持一致；视频、音频 references 仍走 URL。

需要在内网部署、且未配置 S3/R2 公开地址的环境下验证：使用参考图或首尾帧生成视频不再出现 `URL地址不合法/不存在` 这类间歇性失败。
