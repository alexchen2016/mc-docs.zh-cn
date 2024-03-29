---
title: 示例 - Azure 事件中心 | Azure Docs
description: 本文提供了 GitHub 上 Azure 事件中心的示例列表。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 07/17/2018
ms.date: 05/06/2019
ms.author: v-biyu
ms.openlocfilehash: e16ed82ed4301be3a83ffb533b9003005d2de9dc
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854750"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>带有 Azure 事件中心示例的 Git 存储库 
可以在 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples) 上找到事件中心示例。 这些示例演示了 [Azure 事件中心](/event-hubs/)的主要功能。 本文对可用示例进行了分类和介绍，每个示例均具有链接。

## <a name="net-samples"></a>.NET 示例

| 示例名称 | 说明 | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | 此示例演示如何编写将一组事件发送到事件中心的 .NET Core 控制台应用程序。 |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | 此示例介绍如何编写 .NET Core 控制台应用程序，以使用事件处理程序主机库从事件中心接收事件。  | 

## <a name="java-samples"></a>Java 示例

| 示例名称 | 说明 | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | 此示例说明了如何将批量事件引入到事件中心。 | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | 此示例说明了如何将事件引入到事件中心。 |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | 此示例说明了可用于事件中心以引入事件的选项。 |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | 此示例说明了如何使用特定的日期时间偏移量从事件中心分区接收事件。 |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | 此示例说明了如何使用特定的数据偏移量从事件中心分区接收事件。 |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | 此示例说明了如何使用序列号从事件中心分区进行接收。 |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |此示例说明了如何使用事件处理程序主机从事件中心接收事件，其中该主机提供跨多个并发接收器的自动分区选择和故障转移功能。 | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | 此示例说明了事件中心如何自动在高负载中纵向扩展。 该示例将以刚好超过事件中心配置速率的速率发送事件，使导致事件中心纵向扩展。 | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | 此示例允许测量入口速率。 | 

## <a name="python-samples"></a>Python 示例
可在 [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) GitHub 存储库中找到 Azure 事件中心的 Python 示例。

## <a name="nodejs-samples"></a>Node.js 示例
可在 [azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) GitHub 存储库中找到 Azure 事件中心的 Node.js 示例。

## <a name="go-samples"></a>Go 示例
可在 [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) GitHub 存储库中找到 Azure 事件中心的 Go 示例。

## <a name="azure-cli-samples"></a>Azure CLI 示例
可在 [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) GitHub 存储库中找到 Azure 事件中心的 Azure CLI 示例。

## <a name="azure-powershell-samples"></a>Azure PowerShell 示例
可在 [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) GitHub 存储库中找到 Azure 事件中心的 Azure PowerShell 示例。
## <a name="next-steps"></a>后续步骤
可在以下文章中了解有关事件中心的详细信息：

- [事件中心概述](event-hubs-what-is-event-hubs.md)
- [事件中心功能](event-hubs-features.md)
- [事件中心常见问题](event-hubs-faq.md)
