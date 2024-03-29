---
title: 授权代码流 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何使用 Azure AD B2C 和 OpenID Connect 身份验证协议生成 Web 应用。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 02/19/2019
ms.date: 04/02/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 33529408a5c7f9c7b92f6c2405597a4ed9e325b0
ms.sourcegitcommit: 623e8f0d52c42d236ad2a0136d5aebd6528dbee3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67235956"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 OAuth 2.0 授权代码流

可使用 OAuth 2.0 授权代码向设备上安装的应用授权，获取访问受保护资源（例如 Web API）的权限。 通过使用 OAuth 2.0 的 Azure Active Directory B2C (Azure AD B2C) 实现，可向移动应用和桌面应用添加注册、登录和其他标识管理任务。 本文与语言无关。 本文介绍在不使用任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

[OAuth 2.0 规范第 4.1 部分](https://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权代码流。 可在大多数[应用程序类型](active-directory-b2c-apps.md)中将其用于身份验证和授权，包括 Web 应用和本机安装的应用程序。 可使用 OAuth 2.0 授权代码流安全地获取应用程序的访问令牌和刷新令牌，这些令牌可用于访问受到[授权服务器](active-directory-b2c-reference-protocols.md)保护的资源。  刷新令牌允许客户端在访问令牌到期后（通常在一小时后）获取新的访问（和刷新）令牌。

本文重点介绍**公共客户端** OAuth 2.0 授权代码流。 公共客户端是那些不能被信任以安全维护机密密码完整性的任何客户端应用程序。 这包括移动应用、桌面应用程序，以及在设备上运行并需要获取访问令牌的几乎所有应用程序。 

> [!NOTE]
> 若要使用 Azure AD B2C 向 Web 应用添加标识管理，请使用 [OpenID Connect](active-directory-b2c-reference-oidc.md)，而不要使用 OAuth 2.0。

Azure AD B2C 扩展了标准 OAuth 2.0 流，使其功能远远超出了简单的身份验证和授权。 它引入了[用户流参数](active-directory-b2c-reference-policies.md)。 借助用户流，可使用 OAuth 2.0 向应用程序添加用户体验，例如注册、登录和配置文件管理。 使用 OAuth 2.0 协议的标识提供商包括 [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md)、[GitHub](active-directory-b2c-setup-github-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md)。

在本文的示例 HTTP 请求中，我们使用示例 Azure AD B2C 目录 **fabrikamb2c.partner.onmschina.cn**。 我们还使用示例应用程序和用户流。 你可以使用这些值亲自尝试这些请求，或者可以用自己的值替换它们。
了解如何[获取自己的 Azure AD B2C 目录、应用程序和用户流](#use-your-own-azure-ad-b2c-directory)。

## <a name="1-get-an-authorization-code"></a>1.获取授权代码
授权代码流始于客户端将用户定向到 `/authorize` 终结点。 这是授权代码流中用户会执行操作的交互部分。 在此请求中，客户端指示在 `scope` 参数中需要从用户处获取的权限。 在 `p` 参数中，它指示要执行的用户流。 下面有三个示例（为方便阅读，提供了换行符），每个示例都使用不同的用户流。

### <a name="use-a-sign-in-user-flow"></a>使用登录用户流
```
GET https://fabrikamb2c.b2clogin.cn/fabrikamb2c.partner.onmschina.cn/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>使用注册用户流
```
GET https://fabrikamb2c.b2clogin.cn/fabrikamb2c.partner.onmschina.cn/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>使用编辑配置文件用户流
```
GET https://fabrikamb2c.b2clogin.cn/fabrikamb2c.partner.onmschina.cn/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| client_id |必须 |在 [Azure 门户](https://portal.azure.cn)中分配给应用的应用程序 ID。 |
| response_type |必须 |响应类型，其中必须包括 `code` 的授权待码流。 |
| redirect_uri |必须 |应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 它必须完全匹配在门户中注册的其中一个重定向 URI，但必须经 URL 编码。 |
| scope |必须 |范围的空格分隔列表。 一个范围值可向 Azure Active Directory (Azure AD) 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。 还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| response_mode |建议 |用于将生成的授权代码发回应用的方法。 可以是 `query`、`form_post` 或 `fragment`。 |
| state |建议 |请求中包含的值，可以是要使用的任何内容的字符串。 随机生成的唯一值通常用于防止跨网站请求伪造攻击。 它还可用于在身份验证请求发生前，对有关用户在应用中的状态信息进行编码。 例如，用户所处的页面或要执行的用户流。 |
| p |必须 |执行的用户流。 它是在 Azure AD B2C 目录中创建的用户流的名称。 用户流名称值应以 b2c\_1\_ 开头  。 若要了解有关用户流的详细信息，请参阅 [Azure AD B2C 用户流](active-directory-b2c-reference-policies.md)。 |
| prompt |可选 |需要的用户交互类型。 目前，唯一有效的值为 `login`，这会强制用户在该请求中输入其凭据。 单一登录不会生效。 |

此时，要求用户完成用户流的工作流。 这可能涉及用户输入其用户名和密码、使用社交标识登录、注册目录，或任何其他步骤。 用户操作取决于用户流是如何定义的。

用户完成用户流后，Azure AD 会在你用于 `redirect_uri` 的值处将响应返回到应用。 它使用在 `response_mode` 参数中指定的方法。 对于每种用户操作情况，响应完全相同，与执行的用户流无关。

使用 `response_mode=query` 的成功响应如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 参数 | 说明 |
| --- | --- |
| code |应用请求的授权代码。 应用可使用授权代码请求目标资源的访问令牌。 授权代码的生存期非常短。 通常，它们在约 10 分钟后过期。 |
| state |请参阅上一部分的表中的完整说明。 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |

错误响应也可能发送到重定向 URI，让应用能够对其进行适当处理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |帮助识别身份验证错误根本原因的特定错误消息。 |
| state |请参阅上表中的完整说明。 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 `state` 值是否相同。 |

## <a name="2-get-a-token"></a>2.获取令牌
现在，已获取授权代码，可将 POST 请求发送到 `/token` 终结点，兑换 `code` 来获取所需资源的令牌。 在 Azure AD B2C 中，可请求令牌的唯一资源是应用自己的后端 Web API。 用于向自己请求令牌的约定为，使用应用的客户端 ID 作为范围：

```
POST fabrikamb2c.partner.onmschina.cn/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.cn
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必须 |用于获取授权代码的用户流。 无法在此请求中使用不同的用户流。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |必须 |在 [Azure 门户](https://portal.azure.cn)中分配给应用的应用程序 ID。 |
| grant_type |必须 |授权的类型。 对于授权代码流，授权类型必须为 `authorization_code`。 |
| scope |建议 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。  还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| code |必须 |在流的第一个阶段获取的授权代码。 |
| redirect_uri |必须 |在其中收到授权代码的应用程序的重定向 URI。 |

成功令牌响应如下所示：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 参数 | 说明 |
| --- | --- |
| not_before |epoch 时间中令牌被视为有效的时间。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| access_token |所请求的已签名 JSON Web 令牌 (JWT)。 |
| scope |令牌的有效范围。 还可使用范围缓存令牌以备后用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌的生存期较长。 可使用它们长期保留对资源的访问权限。 有关详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |帮助识别身份验证错误根本原因的特定错误消息。 |

## <a name="3-use-the-token"></a>3.使用令牌
现在，已成功获取访问令牌，可通过在 `Authorization` 标头中包含令牌，在后端 Web API 请求中使用令牌：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.刷新令牌
访问令牌和 ID 令牌的生存期较短。 过期后，必须将其刷新才能继续访问资源。 若要执行此操作，请向 `/token` 终结点提交另一个 POST 请求。 这次提供的是 `refresh_token` 而不是 `code`：

```
POST fabrikamb2c.partner.onmschina.cn/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.cn
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必须 |用于获取原始刷新令牌的用户流。 无法在此请求中使用不同的用户流。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |必须 |在 [Azure 门户](https://portal.azure.cn)中分配给应用的应用程序 ID。 |
| client_secret |必须 |在 [Azure 门户](https://portal.azure.cn)中关联到 client_id 的 client_secret。 |
| grant_type |必须 |授权的类型。 对于授权代码流的此阶段，授权类型必须为 `refresh_token`。 |
| scope |建议 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。  还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| redirect_uri |可选 |在其中收到授权代码的应用程序的重定向 URI。 |
| refresh_token |必须 |在授权代码流的第二个阶段获取的原始刷新令牌。 |

成功令牌响应如下所示：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 参数 | 说明 |
| --- | --- |
| not_before |epoch 时间中令牌被视为有效的时间。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| access_token |所请求的已签名 JWT。 |
| scope |令牌的有效范围。 还可使用范围缓存令牌以备后用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |帮助识别身份验证错误根本原因的特定错误消息。 |

## <a name="use-your-own-azure-ad-b2c-directory"></a>使用自己的 Azure AD B2C 目录
若要自行尝试这些请求，请完成以下步骤。 使用自己的值替换本文中使用的示例值。

1. [创建 Azure AD B2C 目录](active-directory-b2c-get-started.md)。 在请求中使用目录的名称。
2. [创建应用程序](active-directory-b2c-app-registration.md)，获取应用程序 ID 和重定向 URI。 在应用中包含本机客户端。
3. [创建用户流](active-directory-b2c-reference-policies.md)以获取用户流名称。


