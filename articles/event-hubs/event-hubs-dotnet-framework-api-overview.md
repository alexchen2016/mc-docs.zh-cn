---
title: Azure 事件中心 .NET Framework API 概述 | Azure
description: 汇总了一些重要的事件中心 .NET Framework 客户端 API。
services: event-hubs
author: rockboyfor
manager: digimobile
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
origin.date: 08/16/2018
ms.date: 09/30/2018
ms.author: v-yeche
ms.openlocfilehash: 7966eccf53d6b3bd40561094181c5180f29322c4
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52658860"
---
# <a name="event-hubs-net-framework-api-overview"></a>事件中心 .NET Framework API 概述

本文汇总了一些重要的 Azure 事件中心 [.NET Framework 客户端 API](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)。 有两个类别：管理 API 和运行时 API。 运行时 API 包括发送和接收消息所需的全部操作。 借助管理操作，可以通过创建、更新和删除实体来管理事件中心实体状态。

[监视方案](event-hubs-metrics-azure-monitor.md)跨越管理和运行时。 有关 .NET API 的详细参考文档，请参阅 [.NET Framework](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.servicebus.messaging.eventhubclient?view=azure-dotnet)、[.NET Standard](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.eventhubs?view=azure-dotnet) 和 [EventProcessorHost API](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.eventhubs.processor?view=azure-dotnet) 参考。

## <a name="management-apis"></a>管理 API

若要执行以下管理操作，必须对事件中心命名空间具有 **管理** 权限：

### <a name="create"></a>创建

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>更新

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Delete

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>运行时 API
### <a name="create-publisher"></a>创建发布者

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>发布消息

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>创建使用者

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>使用消息

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>事件处理程序主机 API

这些 API 通过在可用工作进程之间分布分区，为可能变为不可用的工作进程提供复原能力。

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

[IEventProcessor](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.servicebus.messaging.ieventprocessor?view=azure-dotnet) 接口定义如下：

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

若要了解有关事件中心方案的详细信息，请访问以下链接：

* [什么是 Azure 事件中心？](event-hubs-what-is-event-hubs.md)
* [事件中心编程指南](event-hubs-programming-guide.md)

下面提供了 .NET API 参考：

* [Microsoft.ServiceBus.Messaging](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.servicebus.messaging?view=azure-dotnet)
* [Microsoft.Azure.EventHubs.EventProcessorHost](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost?view=azure-dotnet)

<!--Update_Description: update meta properties -->