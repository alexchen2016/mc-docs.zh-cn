---
title: 教程 - 通过 Azure PowerShell 使用发布/订阅渠道和主题筛选器更新零售库存分类 | Azure
description: 在本教程中，你将了解如何从主题和订阅发送和接收消息，以及如何使用 Azure PowerShell 添加和使用筛选器规则
services: service-bus-messaging
author: lingliw
manager: digimobile
ms.author: v-lingwu
origin.date: 09/22/2018
ms.date: 11/26/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 91ea2cd121d93ae26ab96962a0d3f8201062019e
ms.sourcegitcommit: 884c387780131bfa2aab0e54d177cb61ad7070a3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65609858"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>教程：使用 PowerShell 和主题/订阅更新库存

Azure 服务总线是一种多租户云消息传送服务，可以在应用程序和服务之间发送信息。 异步操作可实现灵活的中转消息传送、结构化的先进先出 (FIFO) 消息传送以及发布/订阅功能。 

本教程展示了如何使用 PowerShell 创建消息命名空间并在该命名空间中创建队列，以及如何获取该命名空间上的授权凭据，以便将消息发送到服务总线队列及从中接收消息。 然后该过程展示了如何使用 [.NET Standard 库](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)从此队列发送和接收消息。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 使用 Azure PowerShell 创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 PowerShell 添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

此方案的一个示例是为多个零售店更新库存分类。 在此方案中，每个商店或商店组都获取适用于它们的消息来更新其分类。 本教程展示了如何使用订阅和筛选器实现此方案。 首先，创建一个包含 3 个订阅的主题，添加一些规则和筛选器，然后从主题和订阅发送和接收消息。

![主题](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

如果没有 Azure 订阅，请在开始前创建[试用帐户][]。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已安装：

1. [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](https://www.visualstudio.com/vs)或更高版本。
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

本教程要求运行最新版本的 Azure PowerShell。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell][]。


## <a name="sign-in-to-azure"></a>登录 Azure

发出以下命令来登录到 Azure。 如果是在 Cloud Shell 中运行 PowerShell 命令，则这些步骤不是必需的： 

1. 安装服务总线 PowerShell 模块：

   ```PowerShell
   Install-Module Az.ServiceBus
   ```

2. 运行以下命令来登录到 Azure：

   ```PowerShell
   Connect-AzAccount -Environment AzureChinaCloud
   ```

4. 设置当前的订阅上下文，或者查看当前处于活动状态的订阅：

   ```PowerShell
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>预配资源

登录到 Azure 后，发出以下命令来预配服务总线资源。 请务必将所有占位符替换为适当的值：

```PowerShell
# Create a resource group 
New-AzesourceGroup -Name my-resourcegroup -Location chinanorth2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location chinanorth2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

在 `Get-AzServiceBusKey` cmdlet 运行后，将所选的连接字符串和队列名称复制并粘贴到一个临时位置，例如记事本。 在下一步中将要使用它。

## <a name="send-and-receive-messages"></a>发送和接收消息

创建命名空间和队列并且拥有所需的凭据后，便可以发送和接收消息。 可以观察[此 GitHub 示例文件夹](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart)中的代码。

若要运行此代码，请执行以下操作：

1. 通过发出以下命令克隆[服务总线 GitHub 存储库](https://github.com/Azure/azure-service-bus/)：

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 打开 PowerShell 提示符。

3. 导航到示例文件夹 `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`。

4. 如果尚未这样做，请使用以下 PowerShell cmdlet 获取连接字符串。 请务必将 `my-resourcegroup` 和 `namespace-name` 替换为具体值： 

   ```PowerShell
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. 在 PowerShell 提示符下，键入以下命令：

   ```shell
   dotnet build
   ```
6. 导航到 `\bin\Debug\netcoreapp2.0` 文件夹。
7. 键入以下命令以运行程序。 请务必将 `myConnectionString` 替换为先前获取的值，将 `myQueueName` 替换为所创建队列的名称：

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. 观察发送到队列并随后从队列中接收的 10 条消息：

   ![程序输出](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>清理资源

运行以下命令来删除资源组、命名空间和所有相关资源：

```PowerShell
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>了解示例代码

此部分包含有关示例代码功能的更多详细信息。 

### <a name="get-connection-string-and-queue"></a>获取连接字符串和队列

连接字符串和队列名称作为命令行参数传递给 `Main()` 方法。 `Main()` 声明了两个字符串变量来保存这些值：

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
然后，`Main()` 方法启动异步消息循环 `MainAsync()`。

### <a name="message-loop"></a>消息循环

`MainAsync()` 方法使用命令行参数创建队列客户端，调用名为 `RegisterOnMessageHandlerAndReceiveMessages()` 的接收消息处理程序，并发送消息集：

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

`RegisterOnMessageHandlerAndReceiveMessages()` 方法设置一些消息处理程序选项，然后调用队列客户端的 `RegisterMessageHandler()` 方法，该方法开始接收消息：

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>发送消息

消息创建和发送操作发生在 `SendMessagesAsync()` 方法中：

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>处理消息

`ProcessMessagesAsync()` 方法确认、处理并完成消息的接收：

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure PowerShell 预配了资源，然后从服务总线主题及其订阅发送并接收了消息。 你已了解如何：

> [!div class="checklist"]
> * 使用 Azure 门户创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 .NET 代码添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

若要通过更多示例来了解如何发送和接收消息，请从 [GitHub 上的服务总线示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)着手。

若要详细了解如何使用服务总线的发布/订阅功能，请转到下一教程。

> [!div class="nextstepaction"]
> [使用 PowerShell 和主题/订阅更新库存](service-bus-tutorial-topics-subscriptions-cli.md)

[试用帐户]: https://www.azure.cn/pricing/1rmb-trial/
[安装和配置 Azure PowerShell]: https://docs.azure.cn/zh-cn/powershell-install-configure