---
title: 使用 .NET Core 接收事件 - Azure 事件中心 | Azure Docs
description: 本文提供了一个演练，用于创建使用 EventProcessorHost 接收消息的 .NET Core 应用程序。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 08/16/2018
ms.date: 04/08/2019
ms.author: v-biyu
ms.openlocfilehash: 699410d024d18026d79b3ba1b92c55aad0653b55
ms.sourcegitcommit: c5599eb7dfe9fd5fe725b82a861c97605635a73f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505478"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-core"></a>使用 .NET Core 中的事件处理程序主机接收消息入门
事件中心是一个服务，可用于处理来自连接设备和应用程序的大量事件数据（遥测）。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。 这种大规模事件收集和处理功能是现代应用程序体系结构（包括物联网 (IoT)）的重要组件。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程显示如何编写 .NET Core 控制台应用程序，以使用[事件处理程序主机](event-hubs-event-processor-host.md)从事件中心接收消息。 [事件处理程序主机](event-hubs-event-processor-host.md)是一个 .NET 类，它通过从事件中心管理持久检查点和并行接收来简化从那些事件中心接收事件的过程。 使用事件处理程序主机，可跨多个接收方拆分事件，即使在不同节点中托管时也是如此。 此示例演示如何为单一接收方使用事件处理程序主机。 [扩大事件处理][使用事件中心扩大事件处理]示例显示如何将事件处理程序主机用于多个接收方。

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) 下载此用作示例的快速入门，将 `EventHubConnectionString`、`EventHubName`、`StorageAccountName`、`StorageAccountKey`、`StorageContainerName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件
* [Microsoft Visual Studio 2015 或 2017](https://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017，但也支持 Visual Studio 2015。
* [.NET Core Visual Studio 2015 或 2017 工具](https://www.microsoft.com/net/core)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.cn)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的步骤进行操作，然后继续执行本教程的以下步骤。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio。 在“文件”菜单中，单击“新建”，并单击“项目”。 创建 .NET Core 控制台应用程序。

![新建项目][2]

## <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

遵循以下步骤，将 [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 和 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard 库 NuGet 包添加项目： 

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，然后搜索“Microsoft.Azure.EventHubs”，并选择“Microsoft.Azure.EventHubs”包。 单击“安装”以完成安装，并关闭此对话框。
3. 重复步骤 1 和步骤 2，安装“Microsoft.Azure.EventHubs.Processor”包。

## <a name="implement-the-ieventprocessor-interface"></a>实现 IEventProcessor 接口

1. 在“解决方案资源管理器”中，右键单击该项目，单击“添加”，并单击“类”。 将新类命名为 **SimpleEventProcessor**。

2. 打开 SimpleEventProcessor.cs 文件，并将以下 `using` 语句添加到文件顶部。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. 实现 `IEventProcessor` 接口。 将 `SimpleEventProcessor` 类的全部内容替换为以下代码：

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>更新 Main 方法以使用 SimpleEventProcessor

1. 在 Program.cs 文件顶部添加以下 `using` 语句。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 向 `Program` 类添加常量作为事件中心连接字符串、事件中心名称、存储帐户容器名称、存储帐户名称和存储帐户密钥。 添加以下代码，并将占位符替换为其对应的值：

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 将名为 `MainAsync` 的新方法添加到 `Program` 类，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 在 `Main` 方法中添加以下代码行：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 文件的内容如下所示：

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1};EndpointSuffix=core.chinacloudapi.cn", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
    <!-- Add ;EndpointSuffix=core.chinacloudapi.cn in connectionString -->

4. 运行程序，并确保没有任何错误。

祝贺你！ 现在已使用事件处理器主机从事件中心接收消息。

> [!NOTE]
> 本教程使用单个 [EventProcessorHost](event-hubs-event-processor-host.md) 实例。 若要增加吞吐量，建议运行多个 [EventProcessorHost](event-hubs-event-processor-host.md) 实例，如[扩展的事件处理](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)示例中所示。 在这些情况下，为了对接收的事件进行负载均衡，多个实例会自动相互协调。 

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已创建从事件中心接收消息的 .NET Core 应用程序。 若要了解如何使用 .NET Core 将事件发送到事件中心，请参阅[从事件中心发送事件 - .NET Core](event-hubs-dotnet-standard-getstarted-send.md)。


[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
