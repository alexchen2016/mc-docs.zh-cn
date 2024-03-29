---
title: WebJobs SDK 入门 - Azure
description: 用于事件驱动的后台处理的 WebJobs SDK 简介。 了解如何访问 Azure 服务和第三方服务中的数据。
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 04/27/2018
ms.date: 06/17/2019
ms.author: v-biyu
ms.openlocfilehash: f1611f2da169265bf63dc8720853ca2626227197
ms.sourcegitcommit: d7db02d1b62c7b4deebd5989be97326b4425d1d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687428"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>用于事件驱动的后台处理的 Azure WebJobs SDK 入门

本文介绍如何使用 Visual Studio 2019 创建 Azure WebJobs SDK 项目、在本地运行它，然后将其部署到 [Azure 应用服务](overview.md)。 WebJobs SDK 的 3.x 版同时支持 .NET Core 和 .NET Framework 控制台应用。 若要详细了解如何使用 WebJobs SDK，请参阅[如何使用 Azure WebJobs SDK 进行事件驱动的后台处理](webjobs-sdk-how-to.md)。

本文介绍如何将 WebJobs 部署为 .NET Core 控制台应用。 若要将 WebJobs 部署为 .NET Framework 控制台应用，请参阅 [WebJobs 作为 .NET Framework 控制台应用](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps)。 如果你对仅支持 .NET Framework 的 WebJobs SDK 版本 2.x 感兴趣，请参阅[使用 Visual Studio 开发和部署 WebJob - Azure 应用服务](webjobs-dotnet-deploy-vs.md)。

## <a name="prerequisites"></a>先决条件

* [安装](https://docs.microsoft.com/visualstudio/install/)包含 **Azure 开发**工作负荷的 Visual Studio 2017。 如果已安装 Visual Studio，但未配置该工作负荷，请选择“工具”>“获取工具和功能”添加该工作负荷。 

* 必须有一个 [Azure 帐户](https://www.azure.cn/pricing/1rmb-trial)才能将 WebJobs SDK 项目发布到 Azure。

## <a name="create-a-project"></a>创建一个项目

1. 在 Visual Studio 中，选择“新建项目”  。

2. 选择“控制台应用(.NET Core)”  。

3. 将项目命名为 *WebJobsSDKSample*，然后选择“创建”。 

   ![“新建项目”对话框](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet 包

1. 安装以下 NuGet 包的最新稳定版本 3.x 版：

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     下面是适用于版本 3.0.4 的**包管理器控制台**命令：

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>创建主机

主机是函数的运行时容器，它侦听触发器并调用函数。 以下步骤创建一个实现 [`IHost`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihost) 的主机，它是 ASP.NET Core 中的通用主机。

1. 在 *Program.cs* 中，添加 `using` 语句：

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. 将 `Main` 方法替换为以下代码：

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

在 ASP.NET Core 中，通过调用 [`HostBuilder`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuilder) 实例上的方法来设置主机配置。 有关详细信息，请参阅 [.NET 通用主机](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host)。 `ConfigureWebJobs` 扩展方法初始化 WebJobs 主机。 在 `ConfigureWebJobs` 中，初始化特定的 WebJobs 扩展并设置这些扩展的属性。  

## <a name="enable-console-logging"></a>启用控制台日志记录

在本部分，设置使用 [ASP.NET Core 日志记录框架](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging)的控制台日志记录。

1. 安装以下 NuGet 包的最新稳定版本：

   * `Microsoft.Extensions.Logging` - 日志记录框架。
   * `Microsoft.Extensions.Logging.Console` - 用于将日志发送到控制台的控制台提供程序。

   下面是 2.2.0 版的“包管理器控制台”命令  ：

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. 在 *Program.cs* 中，添加 `using` 语句：

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. 在 [`HostBuilder`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.hosting.hostbuilder) 上调用 [`ConfigureLogging`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 方法。 [`AddConsole`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) 方法将控制台日志记录添加到配置中。

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    现在，`Main` 方法如下所示：

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    此项更新执行以下操作：

    * 禁用[仪表板日志记录](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)。 仪表板是一个旧式监视工具，不建议对高吞吐量生产方案使用仪表板日志记录。
    * 使用默认[筛选](webjobs-sdk-how-to.md#log-filtering)添加控制台提供程序。

现在即可以添加由到达 [Azure 存储队列](../azure-functions/functions-bindings-storage-queue.md)的消息触发的函数。

## <a name="install-the-storage-binding-extension"></a>安装存储绑定扩展

从版本 3.x 开始，必须显式安装 WebJobs SDK 所需的存储绑定扩展。 在以前版本中，存储绑定已包含在 SDK 中。

1. 安装 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 包的最新稳定版本，即 3.x 版。 

    下面是适用于版本 3.0.3 的**包管理器控制台**命令：

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. 在 `ConfigureWebJobs` 扩展方法中，调用 [`HostBuilder`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.hosting.hostbuilder) 实例上的 `AddAzureStorage` 方法来初始化存储扩展。 此时，`ConfigureWebJobs` 方法如下例所示：

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>创建函数

1. 右键单击项目，选择“添加” > “新建项...”，选择“类”，将新的 C# 类文件命名为 *Functions.cs*，然后选择“添加”。    

1. 在 Functions.cs 中，使用以下代码替换生成的模板：

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` 特性告知运行时，在名为 `queue` 的 Azure 存储队列中写入新消息时，应调用此函数。 队列消息的内容将提供给 `message` 参数中的方法代码。 在方法的正文中处理触发器数据。 在此示例中，代码只是记录消息。

   `message` 参数不一定要是字符串。 也可以绑定到 JSON 对象、字节数组或 [CloudQueueMessage](https://docs.azure.cn/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 对象。 [参阅队列触发器用法](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 每个绑定类型（例如队列、Blob 或表）具有一组可以绑定到的不同参数类型。

## <a name="create-a-storage-account"></a>创建存储帐户

在本地运行的 Azure 存储模拟器不具备 WebJobs SDK 所需的全部功能。 因此，在本部分，我们应在 Azure 中创建一个存储帐户，并将项目配置为使用该帐户。 如果已有一个存储帐户，请跳到步骤 6。

1. 在 Visual Studio 中打开“服务器资源管理器”  并登录 Azure。 右键单击“Azure”节点，选择“连接到 Microsoft Azure 订阅”。  

   ![登录 Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. 在“服务器资源管理器”中的“Azure”节点下，右键单击“存储”并选择“创建存储帐户”。    

   ![“创建存储帐户”菜单](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. 在“创建存储帐户”对话框中，输入存储帐户的唯一名称。 

1. 选择在其中创建了应用服务应用的同一**区域**或者靠近的区域。

1. 选择“创建”  。

   ![创建存储帐户](./media/webjobs-sdk-get-started/create-storage-account.png)

1. 在“服务器资源管理器”中的“存储”节点下，选择新存储帐户。   在“属性”窗口中，选择“连接字符串”值字段右侧的省略号 ( **...** )。  

   ![“连接字符串”右侧的省略号](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 复制连接字符串并将此值保存在某处，以方便再次复制。

   ![复制连接字符串](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>将存储配置为在本地运行

WebJobs SDK 在 Azure 的“应用程序设置”中查找存储连接字符串。 在本地运行时，它会在本地配置文件或环境变量中查找此值。

1. 右键单击项目，选择“添加” > “新建项...”，选择“JavaScript JSON 配置文件”，将新文件命名为 *appsettings.json*，然后选择“添加”。     

1. 在新文件中添加 `AzureWebJobsStorage` 字段，如以下示例所示：

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. 将 *{storage connection string}* 替换为先前复制的连接字符串。

1. 在解决方案资源管理器中选择“appsettings.json”文件，在“属性”窗口中，将“复制到输出目录”设置为“如果较新则复制”。    

稍后，你将在 Azure 应用服务中的应用中添加相同的连接字符串应用设置。

## <a name="test-locally"></a>本地测试

在本部分，我们将生成并在本地运行项目，然后通过创建队列消息来触发函数。

1. 按 **Ctrl+F5** 运行项目。

   控制台显示运行时已找到函数，并等待队列消息触发该函数。 v3.x 主机生成以下输出：

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

2. 关闭控制台窗口。

3. 在 Visual Studio 的“服务器资源管理器”中，展开新存储帐户所在的节点，然后右键单击“队列”。  

4. 选择“创建队列”。 

5. 输入 *queue* 作为队列名称，然后选择“确定”。 

   ![创建队列](./media/webjobs-sdk-get-started/create-queue.png)

6. 右键单击新队列所在的节点，然后选择“查看队列”。 

7. 选择“添加消息”图标。 

   ![创建队列](./media/webjobs-sdk-get-started/create-queue-message.png)

8. 在“添加消息”对话框中，输入 *Hello World!*  作为**消息正文**，然后选择“确定”。  现在，队列中会出现一条消息。

   ![创建队列](./media/webjobs-sdk-get-started/hello-world-text.png)

9. 再次运行该项目。

   由于在 `ProcessQueueMessage` 函数中使用了 `QueueTrigger` 特性，因此 WeJobs SDK 运行时会在启动时侦听队列消息。 它会在名为 *queue* 的队列中查找新队列消息，并调用函数。

   由于[队列轮询指数退让](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)，运行时最长可能需要花费 2 分钟才能找到消息并调用函数。 以[开发模式](webjobs-sdk-how-to.md#host-development-settings)运行可以缩减此等待时间。

   控制台输出如下所示：

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

10. 关闭控制台窗口。 

11. 返回“队列”窗口并刷新。 该消息已消失，因为本地运行的函数已对其进行处理。 

## <a name="deploy-as-a-webjob"></a>部署 WebJob

在本部分中，我们将项目部署为 WebJob。 将项目部署到前面创建的应用服务应用。 为了在 Azure 中运行项目时测试代码，我们将通过创建一条队列消息来触发函数调用。

1. 在“解决方案资源管理器”中，右键单击项目并选择“发布为 Azure WebJob”。  

2. 在“添加 Azure WebJob”对话框中，选择“确定”。  

   ![添加 Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio 会自动安装用于 WebJob 发布的 NuGet 包。

3. 在“发布”向导的“配置文件”步骤中，选择“Microsoft Azure 应用服务”    。

   ![“发布”对话框](./media/webjobs-sdk-get-started/publish-dialog.png)

4. 在“应用服务”对话框中，选择“<你的资源组>”>“<你的应用服务应用>”，然后选择“确定”。   

   ![“应用服务”对话框](./media/webjobs-sdk-get-started/app-service-dialog.png)

5. 在向导的“连接”步骤中，选择“发布”。  

## <a name="deploy-as-a-webjob"></a>部署到 Azure

在部署期间，可以创建一个要在其中运行函数的应用服务实例。 将 .NET Core 控制台应用发布到 Azure 中的应用服务时，该应用会自动以 WebJob 的形式运行。 若要详细了解发布过程，请参阅[使用 Visual Studio 开发和部署 WebJob](webjobs-dotnet-deploy-vs.md)。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>触发 Azure 中的函数

1. 确保不是在本地运行（如果控制台窗口仍旧打开，请将其关闭）。 否则，本地实例可能是处理所创建的任何队列消息的第一个实例。

1. 在 Visual Studio 的“队列”页中，像以前一样向队列添加消息  。

1. 刷新“队列”页后新消息消失，因为它已由 Azure 中运行的函数处理  。

   > [!TIP]
   > 若要在 Azure 中进行测试，请使用[开发模式](webjobs-sdk-how-to.md#host-development-settings)来确保立即调用队列触发函数，并避免[队列轮询指数退让](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)导致的延迟。


## <a name="add-an-input-binding"></a>添加输入绑定

输入绑定可以简化读取数据的代码。 在本示例中，队列消息是 Blob 名称，我们将使用该 Blob 名称来查找和读取 Azure 存储中的 Blob。

1. 在 *Functions.cs* 中，将 `ProcessQueueMessage` 方法替换为以下代码：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   在此代码中，`queueTrigger` 是[绑定表达式](https://docs.azure.cn/zh-cn/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns)，意味着它将在运行时解析为不同的值。  在运行时，它会包含队列消息的内容。

2. 添加 `using`：

   ```cs
   using System.IO;
   ```

3. 在存储帐户中创建 Blob 容器。

   a. 在 Visual Studio 的“服务器资源管理器”中，展开你的存储帐户所在的节点，右键单击“Blob”，并选择“创建 Blob 容器”。   

   b. 在“创建 Blob 容器”对话框中，输入 *container* 作为容器名称，然后单击“确定”。  

4. 将 *Program.cs* 文件上传到 Blob 容器。 （此处使用的文件用作示例；可以上传任何文本文件，并使用该文件的名称创建队列消息。）

   a. 在“服务器资源管理器”中，双击创建的容器所在的节点  。

   b. 在“容器”窗口中，选择“上传”按钮   。

   ![Blob 上传按钮](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. 找到并选择“Program.cs”，然后选择“确定”。  

5. 在前面创建的队列中创建队列消息，并使用 *Program.cs* 作为消息的文本。

   ![队列消息 Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 在本地运行项目

   该队列消息会触发函数，而该函数又会读取 Blob 并记录其长度。 控制台输出如下所示：

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>添加输出绑定

输出绑定可以简化写入数据的代码。 本示例在前一个示例的基础上做了修改，它会写入 Blob 的副本，而不是记录其大小。 Blob 存储绑定包含在我们之前安装的 Azure 存储扩展包中。

1. 将 `ProcessQueueMessage` 方法替换为以下代码：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 创建另一个队列消息，并使用 *Program.cs* 作为消息的文本。

1. 在本地运行项目

   该队列消息会触发函数，而该函数又会读取 Blob、记录其长度并创建新 Blob。 控制台输出相同，但在转到 Blob 容器窗口并选择“刷新”时，会看到名为 *copy-Program.cs* 的新 Blob。 

## <a name="republish-the-updates-to-azure"></a>将更新重新发布到 Azure

1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”  。

1. 在“发布”对话框中，确保当前配置文件已选中，然后选择“发布”。   “输出”窗口中会详细显示发布结果。 
 
1. 通过再次将某个文件上传到 Blob 容器，并将一条消息添加到与所上传文件同名的队列，来验证 Azure 中的函数。 将会看到，该消息已从队列中删除，并且 Blob 容器中创建了该文件的副本。 

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建、运行和部署 WebJobs SDK 3.x 项目。

> [!div class="nextstepaction"]
> [详细了解 WebJobs SDK](webjobs-sdk-how-to.md)
