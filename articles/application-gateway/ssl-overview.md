---
title: 在 Azure 应用程序网关上启用端到端 SSL
description: 本文概述应用程序网关的端到端 SSL 支持。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
origin.date: 03/19/2019
ms.date: 06/12/2019
ms.author: v-junlch
ms.openlocfilehash: 2eaf4a8016b5daf0e2ef1cf9a1a5105cd2fa2c9b
ms.sourcegitcommit: 756a4da01f0af2b26beb17fa398f42cbe7eaf893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67027430"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>应用程序网关的 SSL 终止和端到端 SSL 概述

安全套接字层 (SSL) 是用于在 Web 服务器与浏览器之间建立加密链接的标准安全技术。 此链接可确保在 Web 服务器与浏览器之间传递的所有数据都会得到保密和加密。 应用程序网关支持网关上的 SSL 终止以及端到端的 SSL 加密。

## <a name="ssl-termination"></a>SSL 终止

应用程序网关支持在网关上终止 SSL，之后，流量通常会以未加密状态流到后端服务器。 在应用程序网关上执行 SSL 终止可以带来诸多优势：

- **提高性能** - 执行 SSL 解密时，初次握手最容易造成性能下降。 为了提高性能，执行解密的服务器将会缓存 SSL 会话 ID 并管理 TLS 会话票证。 如果此操作是在应用程序网关上执行的，则来自同一个客户端的所有请求都可以使用缓存的值。 如果此操作是在后端服务器上执行的，则每当客户端的请求转到另一台服务器时，客户端都必须重新进行身份验证。 使用 TLS 票证有助于缓解此问题，但并非所有客户端都支持 TLS 票证，并且配置和管理这些票证可能有难度。
- **更好地利用后端服务器** - SSL/TLS 处理会消耗大量的 CPU 资源，随着密钥大小不断地增大，其消耗的资源会越来越多。 从后端服务器中消除此任务可使它们专注于以最有效的方式提供内容。
- **智能路由** - 解密流量后，应用程序网关便可以访问标头、URI 等请求内容，并可以使用此数据来路由请求。
- **证书管理** - 只需在应用程序网关上购买并安装证书，所有后端服务器不需要证书。 这可以节省时间和开支。

若要配置 SSL 终止，需将一个 SSL 证书添加到侦听器，使应用程序网关能够根据 SSL 协议规范派生对称密钥。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 SSL 证书需采用个人信息交换 (PFX) 格式。 此文件格式适用于导出私钥，后者是应用程序网关对流量进行加解密所必需的。

> [!NOTE] 
>
> 应用程序网关不会提供任何用于创建新证书，或者将证书请求发送到证书颁发机构的功能。

若要建立 SSL 连接，需要确保 SSL 证书符合以下条件：

- 当前日期和时间处于证书上的“Valid from”（有效起始日期）和“Valid to”（有效结束日期）日期范围内。
- 证书的“公用名”(CN) 与请求中的主机标头相匹配。 例如，如果客户端正在向 `https://www.contoso.com/` 发出请求，则 CN 必须是 `www.contoso.com`。

### <a name="certificates-supported-for-ssl-termination"></a>支持用于 SSL 终止的证书

应用程序网关支持以下类型的证书：

- CA（证书颁发机构）证书：CA 证书是证书颁发机构 (CA) 颁发的数字证书
- EV（扩展验证）证书：EV 证书是行业标准证书准则。 使用此类证书会使浏览器地址栏变为绿色，并发布公司名称。
- 通配符证书：此证书支持任意数量的基于 *.site.com 的子域（其中的 * 需替换为你的子域）。 但是，它不支持 site.com，因此，如果用户在不键入前导“www”的情况下访问你的网站，则通配符证书不会反映这一点。
- 自签名证书：客户端浏览器不信任这些证书，并且会警告用户，指出虚拟服务的证书不是信任链的一部分。 自签名证书适合用于测试，或者管理员会在其中控制客户端并且可以安全绕过浏览器安全警报的环境。 切勿将自签名证书用于生产工作负荷。

有关详细信息，请参阅[配置应用程序网关的 SSL 终止](/application-gateway/create-ssl-portal)。

### <a name="size-of-the-certificate"></a>证书大小
查看[应用程序网关限制](/azure-subscription-service-limits#application-gateway-limits)部分，了解支持的最大 SSL 证书大小。

## <a name="end-to-end-ssl-encryption"></a>端到端 SSL 加密

某些客户可能不希望与后端服务器进行未加密的通信。 这可能是因为安全要求、符合性要求，或者应用程序可能仅接受安全连接。 对于此类应用程序，应用程序网关支持端到端 SSL 加密。

使用端到端 SSL 可以安全地将敏感数据以加密方式传输到后端，同时仍可利用应用程序网关提供的第 7 层负载均衡功能的优点。 部分功能包括：基于 Cookie 的会话相关性、基于 URL 的路由、基于站点的路由支持，或注入 X-Forwarded-* 标头。

如果配置为端到端 SSL 通信模式，应用程序网关会在网关上终止 SSL 会话，并解密用户流量。 然后，它会应用配置的规则，以选择要将流量路由到的适当后端池实例。 应用程序网关接下来会初始化到后端服务器的新 SSL 连接，并先使用后端服务器的公钥证书重新加密数据，此后再将请求传输到后端。 来自 Web 服务器的任何响应都会经历相同的过程返回最终用户。 若要启用端到端 SSL，可将 [后端 HTTP 设置](/application-gateway/configuration-overview#http-settings) 中的协议设置为 HTTPS，此设置随后将应用到后端池。

SSL 策略将应用到前端和后端流量。 在前端上，应用程序网关充当服务器并强制实施该策略。 在后端上，应用程序网关充当客户端，并在 SSL 握手期间将协议/密码信息作为首选项发送。

应用程序网关只会与下面所述的后端实例通信：已将其证书加入应用程序网关的白名单，或者其证书已由已知的 CA 颁发机构签名，并且证书 CN 与 HTTP 后端设置中的主机名匹配。 这些实例包括受信任的 Azure 服务，例如 Azure 应用服务 Web 应用和 Azure API 管理。

如果后端池成员的证书未由已知 CA 颁发机构签名，则必须为后端池中已启用端到端 SSL 的每个实例配置一个证书，这样才能进行安全通信。 添加证书后，可确保应用程序网关仅与已知后端实例通信。 从而进一步保护端到端通信。

> [!NOTE] 
>
> Azure 应用服务 Web 应用和 Azure API 管理等受信任的 Azure 服务不需要身份验证证书设置。

> [!NOTE] 
>
> 添加到“后端 HTTP 设置”中的、用于对后端服务器进行身份验证的证书，可以是添加到**侦听器**的、用于在应用程序网关上实现 SSL 终止的同一个证书；为了增强安全性，两者也可以不同。 

![端到端 ssl 方案][1]

在此示例中，使用 TLS1.2 的请求通过端到端 SSL 路由到池 1 中的后端服务器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>端到端 SSL 和证书允许列表

应用程序网关只会与已知的后端实例通信，这些实例已将其证书加入应用程序网关的允许列表。 要启用证书允许列表，必须将后端服务器证书（不是根证书）的公钥上传到应用程序网关。 只允许连接到已知的和列入允许列表的后端。 其余后端会导致网关错误。 自签名证书仅用于测试目的，不建议用于生产工作负荷。 如前面的步骤中所述，此类证书必须加入应用程序网关的允许列表，才可以使用。

> [!NOTE]
> Azure 应用服务等受信任的 Azure 服务不需要身份验证证书设置。

## <a name="next-steps"></a>后续步骤

了解端到端 SSL 后，请转到[使用应用程序网关和 PowerShell 配置端到端 SSL](application-gateway-end-to-end-ssl-powershell.md)，以使用端到端 SSL 创建应用程序网关。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png

<!-- Update_Description: wording update -->