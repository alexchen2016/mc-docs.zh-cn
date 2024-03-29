---
title: Azure 中继 API 概述
description: 可用的 Azure 中继 API 概述
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 05/02/2018
ms.author: v-yiso
ms.date: 06/04/2018
ms.openlocfilehash: 554b163c1d7e4aa44d280788a857143df02ccffb
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52649519"
---
# <a name="available-relay-apis"></a>可用的中继 API

## <a name="runtime-apis"></a>运行时 API

下表列出了当前可用的所有中继运行时客户端。

[其他信息](#additional-information)部分包含有关每个运行时库状态的详细信息。

| 语言/平台 | 可用功能 | 客户端程序包 | 存储库 |
| --- | --- | --- | --- |
| .NET Standard | 混合连接 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET framework | WCF 中继 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 不适用 |
| 节点 | 混合连接 | [Websocket：`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websocket：`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP 请求：`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>其他信息

#### <a name="net"></a>.NET

.NET 生态系统具有多个运行时，因此中继有多个 .NET 库。 可以使用 .NET Core 或 .NET Framework 运行 .NET Standard 库，但 .NET Framework 库只能在 .NET Framework 环境中运行。 有关 .NET Frameworks 的详细信息，请参阅 [framework 版本](/dotnet/articles/standard/frameworks#framework-versions)。

.NET Framework 库仅支持 WCF 编程模型并依赖于基于 WCF `net.tcp` 传输的专有二进制协议。 保留此协议和库是为了实现与现有应用程序的后向兼容性。

.NET Standard 库基于混合连接中继的开放协议定义，该中继以 HTTP 和 WebSocket 为基础。 该库支持通过 Websocket 进行流抽象和用于答复 HTTP 请求的简单请求-响应 API 手势。 [Web API](https://github.com/Azure/azure-relay-dotnet) 示例展示了如何将混合连接与适用于 Web 服务的 ASP.NET Core 进行集成。

#### <a name="nodejs"></a>Node.js

上表中列出的混合连接模块使用在 Azure 中继服务上而非在本地网络堆栈上进行侦听的备用实现替代或修正了现有 Node.js 模块。

`hyco-https` 模块修正并部分替代了核心 Node.js 模块 `http` 和 `https`，提供了一个与依赖于这些核心模块的许多现有 Node.js 模块和应用程序兼容的 HTTPS 侦听器实现。

`hyco-ws` 和 `hyco-websocket` 模块修正了 Node.js 的常用 `ws` 和 `websocket` 模块，提供了备用侦听器实现，这些实现使得依赖于上述任一模块的模块和应用程序能够在混合连接中继后面工作。

可在 [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub 存储库中找到有关这些模块的详细信息。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请访问以下链接：
* [什么是 Azure 中继？](./relay-what-is-it.md)
* [中继常见问题](./relay-faq.md)


<!--Update_Description:update meta properties only-->