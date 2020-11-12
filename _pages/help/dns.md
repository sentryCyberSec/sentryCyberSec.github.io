---
layout: page
title: "TUNA DNS666 域名查询服务"
author: Jason Lau
permalink: /help/dns/
---

TUNA 现提供一台双栈域名递归查询服务器，向校内师生服务，提供域名**准确**的递归查询服务：

# 101.6.6.6 / 2001:da8::666

DNS666 提供 IPv4 与 IPv6 双栈服务——但无论您选择只使用 IPv4 的地址还是选择只使用 IPv6 的地址，都可以同时正常进行 IPv4 (A) 记录和 IPv6 (AAAA) 两种记录的查询，您可以在包括学生宿舍有线或无线网络、教学楼 Tsinghua Wi-Fi 等原生双栈网络下，或是 DIVI 等过渡网络下，正常进行具有 IPv4 和 IPv6 结果的域名查询。清华师生在使用有线网络时，可以使用这一服务进行域名查询，而无需进行校园网认证。

请将系统网络设置中 DNS 项修改为 `101.6.6.6` 和 `2001:da8::666`，即可开始使用。具体配置方式暂缺，请[帮助我们](https://github.com/tuna/tuna.moe)补充文档。

在开始使用这一服务之前，请确保已阅读并理解[使用许可协议](/help/dns-license/)。

<!--
此外，我们还提供如下的查询服务：

### DNS over TLS

DNS over TLS 是一种通过 TLS 加密层次传输 DNS 的协议，规范为 [RFC7858](https://tools.ietf.org/html/rfc7858)。我们在 `dns.tuna.tsinghua.edu.cn:853` 提供该服务。

### DNS over HTTP(S)

本服务与 [Google DNS-over-HTTPS API](https://developers.google.com/speed/public-dns/docs/dns-over-https) 兼容，服务地址为：`https://dns.tuna.tsinghua.edu.cn/resolve`。
-->
