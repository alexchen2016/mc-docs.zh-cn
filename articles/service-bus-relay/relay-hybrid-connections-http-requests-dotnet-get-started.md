---
title: 开始在 .NET 中使用 Azure 中继混合连接 HTTP 请求 | Azure
description: 使用 .NET 为 Azure 中继混合连接 HTTP 请求编写 C# 控制台应用程序。
services: service-bus-relay
documentationcenter: .net
author: lingliw
manager: digimobile
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
origin.date: 11/01/2018
ms.date: 11/26/2018
ms.author: v-lingwu
ms.openlocfilehash: 9f197f2de44697932fa8105449243760b583ef77
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674890"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>开始在 .NET 中使用中继混合连接 HTTP 请求
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

在本快速入门中，请创建 .NET 发送者和接收者应用程序，用于通过 HTTP 协议发送和接收消息。 这些应用程序使用 Azure 中继的混合连接功能。 若要了解 Azure 中继的常规信息，请参阅 [Azure 中继](relay-what-is-it.md)。 

在本快速入门中，你将执行以下步骤：

1. 使用 Azure 门户创建中继命名空间。
2. 使用 Azure 门户在该命名空间中创建混合连接。
3. 编写服务器（侦听器）控制台应用程序，用于接收消息。
4. 编写客户端（发送方）控制台应用程序，用于发送消息。
5. 运行应用程序。 

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要满足以下先决条件：

* [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
* Azure 订阅。 如果没有订阅，请在开始之前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="1-create-a-namespace"></a>1.创建命名空间
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection"></a>2.创建混合连接
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.创建服务器应用程序（侦听程序）
在 Visual Studio 中编写可侦听和接收来自中继的消息的 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.创建客户端应用程序（发送程序）
在 Visual Studio 中编写可将消息发送到中继的 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.运行应用程序
1. 运行服务器应用程序。 可以在控制台窗口中看到以下文本：

    ```
    Online
    Server listening
    ```
1. 运行客户端应用程序。 你将在客户端窗口中看到 `hello!`。 客户端向服务器发送了一个 HTTP 请求，服务器以 `hello!` 进行响应。 
3. 现在，若要关闭控制台窗口，请在两个控制台窗口中按 **ENTER**。 

祝贺你，现已创建端到端混合连接应用程序！

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 .NET 客户端和服务器应用程序，此类程序使用 HTTP 来发送和接收消息。 Azure 中继的混合连接功能也支持使用 WebSocket 发送和接收消息。 若要了解如何将 WebSocket 与 Azure 中继混合连接配合使用，请参阅 [WebSocket 快速入门](relay-hybrid-connections-dotnet-get-started.md)。

在本快速入门中，你使用了 .NET Framework 来创建客户端和服务器应用程序。 若要了解如何使用 Node.js 编写客户端和服务器应用程序，请参阅 [Node.js WebSocket 快速入门](relay-hybrid-connections-node-get-started.md)或 [Node.js HTTP 快速入门](relay-hybrid-connections-http-requests-dotnet-get-started.md)。
<!--Update_Description: update some content -->