---
title: 在本地 Docker 容器中调试应用 | Microsoft Docs
description: 了解如何通过编辑和刷新以及设置调试断点功能来修改本地 Docker 容器中运行的应用以及刷新容器
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
origin.date: 09/11/2018
ms.date: 09/26/2018
ms.author: v-junlch
ms.openlocfilehash: 4f5099dd589e94c0d300f69132c6a0ec6bb332fb
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52645320"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>在本地 Docker 容器中调试应用
## <a name="overview"></a>概述
Visual Studio 2017 提供了在本地 Docker 容器中进行开发以及对应用程序进行验证的一致方法。
每次进行代码更改后，不需要重新启动该容器。
本文将演示如何使用“编辑和刷新”功能在本地 Docker 容器中启动 ASP.NET Core Web 应用，进行任何必要的更改，并刷新浏览器查看这些更改。
本文还将说明如何为调试设置断点。

## <a name="prerequisites"></a>先决条件
必须安装以下工具。

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)（装有“Web 开发”工作负荷）。

若要在本地运行 Docker 容器，需要本地 docker 客户端。
可使用 [Docker 工具箱](https://www.docker.com/products/docker-toolbox)（需禁用 Hyper-V），也可使用 [Docker for Windows](https://www.docker.com/get-docker)（其使用 Hyper-V 并需要 Windows 10）。

如果使用 Docker 工具箱，则需要[配置 Docker 客户端](vs-azure-tools-docker-setup.md)。

## <a name="1-create-a-web-app"></a>1.创建 Web 应用
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2.编辑代码并刷新
要快速重复更改，可以在容器中启动应用程序，并继续进行更改，然后就像使用 IIS Express 一样查看这些更改。

1. 将解决方案配置设置为 `Debug`，并按 **&lt;CTRL + F5>** 以生成 docker 映像并在本地运行它。

    容器映像已生成并在 Docker 容器中运行后，Visual Studio 会在默认浏览器中启动 Web 应用。
    如果使用的是 Microsoft Edge 浏览器或以其他方式出现错误，请参阅 [故障排除](vs-azure-tools-docker-troubleshooting-docker-errors.md) 部分。
2. 请转到“关于”页，我们会在此页中进行更改。
3. 返回到 Visual Studio 并打开 `Views\Home\About.cshtml`。
4. 将以下 HTML 内容添加到文件末尾，并保存更改。

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. 查看输出窗口，当 .NET 生成完成并且你看到这些行时，切换回浏览器并刷新“关于”页。

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. 更改已应用！

## <a name="3-debug-with-breakpoints"></a>3.使用断点进行调试
通常，更改需要利用 Visual Studio 的调试功能进行进一步检查。

1. 返回到 Visual Studio 并打开 `About.cshtml.cs`
2. 将 OnGet() 方法的内容替换为以下代码：

   ```cs
   Message = "Your application description page from within a Container";
   ```

3. 在此代码行的左侧设置一个断点。
4. 按 **&lt;F5>** 开始调试。
5. 导航到“关于”页以命中断点。
6. 切换到 Visual Studio 以查看断点，并检查消息的值。

   ![][2]

## <a name="summary"></a>摘要
借助 Visual Studio 2017 中的 Docker 支持，可以通过在 Docker 容器内进行开发的生产真实性，获得在本地工作的生产效率。

## <a name="troubleshooting"></a>故障排除
[Visual Studio Docker 开发故障排除](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>提供有关在 Visual Studio、Windows 和 Azure 中使用 Docker 的更多信息
- [Azure 管道的 Docker 集成](http://aka.ms/dockertoolsforvsts) - 构建和部署 docker 容器
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) - 用于编辑 docker 文件的语言服务，随后会推出更多 e2e 方案
- [Windows 容器信息](http://aka.ms/containers)- Windows Server 和 Nano Server 信息

## <a name="various-docker-tools"></a>各种 Docker 工具
[一些好用的 docker 工具（Steve Lasker 的博客）](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>优秀文章
[NGINX 中的微服务简介](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>演示
- [Steve Lasker：VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [ASP.NET Core @ build 2016 简介 - 其中你在演示中](https://channel9.msdn.com/Events/Build/2016/B810)
- [在容器中开发 .NET 应用，第 9 频道](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!-- Update_Description: wording update -->