---
title: 使用移动应用在 Android 中添加身份验证
description: 了解如何使用 Azure 应用服务中的移动应用功能通过各种标识提供者（包括 Google、Facebook、Twitter 和 Microsoft）对 Android 应用的用户进行身份验证。
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
origin.date: 11/16/2017
ms.date: 01/29/2018
ms.author: v-yiso
ms.openlocfilehash: 678ff705c5b9e6e4293a95a4f672826e0d205cfb
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663861"
---
# <a name="add-authentication-to-your-android-app"></a>将身份验证添加到 Android 应用
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>摘要
本教程介绍如何使用支持的标识提供者将身份验证添加到 Android 上的待办事项列表快速入门项目。 本教程基于 [Get started with Mobile Apps] （移动应用入门）教程，必须先完成该教程。

## <a name="register"></a>注册应用以进行身份验证并配置 Azure 应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。 此方案允许在完成身份验证过程后，身份验证系统重定向到应用。 在本教程中，我们自始至终使用 URL 方案 _appname_ 。 但是，可以使用任何你所选的 URL 方案。 对于移动应用程序而言，它应是唯一的。 在服务器端启用重定向：

1. 在 [Azure 门户]中，选择应用服务。

2. 单击“身份验证/授权”菜单选项。

3. 在“允许的外部重定向 URL”中，输入 `appname://easyauth.callback`。  此字符串中的 _appname_ 是移动应用程序的 URL 方案。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  应记下此字符串，因为在一些地方需要使用此 URL 方案调整移动应用代码。

4. 单击 **“确定”**。

5. 单击“保存” 。

## <a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* 在 Android Studio 中，打开你按照[Get started with Mobile Apps]教程完成的项目。 从“运行”菜单中单击“运行应用”；验证启动该应用后，是否会引发状态代码为 401（“未授权”）的未经处理的异常。

     发生此异常的原因是应用尝试以未经身份验证的用户身份访问后端，但 TodoItem 表现在要求身份验证。

接下来，需要更新应用，以便在从移动应用后端请求资源之前对用户进行身份验证。 

## <a name="add-authentication-to-the-app"></a>向应用程序添加身份验证
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>在客户端上缓存身份验证令牌
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>后续步骤
完成此基本身份验证教程后，请考虑继续学习以下教程之一：


+ [为 Android 应用启用脱机同步](./app-service-mobile-android-get-started-offline-data.md) 了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步，用户可以与移动应用进行交互&mdash;查看、添加或修改数据&mdash;，即使在没有网络连接时也是如此。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Get started with Mobile Apps]: ./app-service-mobile-android-get-started.md
[Azure 门户]: https://portal.azure.cn/
