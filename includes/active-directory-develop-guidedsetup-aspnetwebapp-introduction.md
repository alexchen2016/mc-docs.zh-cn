---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 04/11/2019
ms.date: 05/10/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 672d9ae770e0eee252f64d0c1d530f87c914c728
ms.sourcegitcommit: 1ebc1e0b99272e62090448d1cd2af385b74ef4b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517635"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>向 ASP.NET Web 应用添加 Microsoft 登录

本指南演示如何通过基于传统 Web 浏览器的使用 OpenID Connect 的应用程序，使用 ASP.NET MVC 解决方案实现 Microsoft 登录。

在本指南结束时，应用程序将能接受使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及与 Azure Active Directory 集成的任何公司或组织的工作和学校帐户进行登录。

> 本指南需要 Visual Studio 2017。  尚未安装？  [免费下载 Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![演示本教程生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

本指南创建的示例应用程序基于这样的场景：用户使用浏览器访问 ASP.NET 网站，该网站要求用户通过登录按钮进行身份验证。 在此方案中，呈现网页的大部分工作在服务器端完成。

## <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|让应用程序可使用 OpenIdConnect 进行身份验证的中间件|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|让应用程序可使用 Cookie 维持用户会话的中间件|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|让基于 OWIN 的应用程序可使用 ASP.NET 请求管道在 IIS 上运行|

