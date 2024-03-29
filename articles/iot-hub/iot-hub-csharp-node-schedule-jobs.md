---
title: 通过 Azure IoT 中心 (.NET/Node) 安排作业 | Azure
description: 如何安排 Azure IoT 中心作业实现多台设备上的直接方法调用。 使用适用于 Node.js 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现用于运行作业的服务应用。
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 07/10/2017
ms.author: v-yiso
ms.date: 05/07/2018
ms.openlocfilehash: dd5806a80faf45fed1efbe2cd38cab316e2804a9
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52652033"
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>计划和广播作业 (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中心来计划和跟踪可更新数百万台设备的作业。 使用作业可以：

* 更新所需属性
* 更新标记
* 调用直接方法

作业包装上述操作之一，并跟踪针对一组设备（由设备孪生查询定义）的执行情况。 例如，后端应用可以使用作业在 10,000 台设备上调用直接方法来重启设备。 使用设备孪生查询指定设备集，并将作业计划为在以后运行。 每个设备接收和执行 reboot 直接方法时，该作业会跟踪进度。

若要详细了解其中的每项功能，请参阅：

* 设备孪生和属性：[设备孪生入门][lnk-get-started-twin]和[教程：如何使用设备孪生属性][lnk-twin-props]
* 直接方法：[IoT 中心开发人员指南 - 直接方法][lnk-dev-methods]和[教程：使用直接方法][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个设备应用，用于实现名为 **lockDoor**、可由后端应用调用的直接方法。 该设备应用还从后端应用接收所需的属性更改。
* 创建一个后端应用，该应用创建一个作业以在多台设备上调用 **lockDoor** 直接方法。 另一个作业将所需的属性更新发送到多台设备。

本教程结束时，用户会有一个 Node.js 控制台设备应用，以及一个 .NET (C#) 控制台后端应用：

**simDevice.js**：用于连接到 IoT 中心，实现 **lockDoor** 直接方法，并处理所需的属性更改。

**ScheduleJob**：使用作业来调用 **lockDoor** 直接方法，并在多台设备上更新设备孪生所需的属性。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 版本 4.0.x 或更高版本。 [准备开发环境][lnk-dev-setup]一文介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。
* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>计划用于调用直接方法和发送设备孪生更新的作业

本部分创建一个 .NET 控制台应用（使用 C#），该应用使用作业来调用 **lockDoor** 直接方法，并将所需属性更新发送到多台设备。

1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 **ScheduleJob**。

    ![新的 Visual C# Windows 经典桌面项目][img-createapp]

1. 在“解决方案资源管理器”中，右键单击“ScheduleJob”项目，并单击“管理 NuGet 包...”。
1. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 此步骤将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口][img-servicenuget]
1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 如果默认语句中不存在下面的 `using` 语句，请添加该语句。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. 将以下字段添加到 **Program** 类。 将占位符替换为上一部分中为中心创建的 IoT 中心连接字符串。

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. 最后，在 **Main** 方法中添加以下行：

    ```csharp
    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 在“解决方案资源管理器”中，打开“设置启动项目...”，并确保 **ScheduleJob** 项目的“操作”为“启动”。 生成解决方案。

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

在本部分中，创建响应云调用的直接方法的 Node.js 控制台应用，这会触发模拟的设备重新启动并使用报告属性启用设备孪生查询，以标识设备和及其上次重新启动的时间。

1. 新建名为 **simDevice**的空文件夹。  在 **simDevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```cmd/sh
    npm init
    ```
1. 在 **simDevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 和 **azure-iot-device-mqtt** 包：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 **simDevice.js** 文件夹中，利用文本编辑器创建新的 **simDevice** 文件。

1. 在 **simDevice.js** 文件的开头添加以下“require”语句：

    ```nodejs
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
1. 添加 **connectionString** 变量，并使用它创建一个**客户端**实例。 请确保使用适合的值替换占位符。

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 添加以下函数以处理 **lockDoor** 方法。

    ```nodejs
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

1. 添加以下代码以注册 **lockDoor** 方法的处理程序。

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. 保存并关闭 **simDevice.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。

## <a name="run-the-apps"></a>运行应用
现在，已准备就绪，可以运行应用。

1. 在 simDevice 文件夹的命令提示符处，运行以下命令以开始侦听重启直接方法。

    ```cmd/sh
    node simDevice.js
    ```
1. 通过右键单击“ScheduleJob”项目，并依次选择“调试”和“启动新实例”，运行 C# 控制台应用 **ScheduleJob**。

1. 会出现设备和后端应用的输出。

    ![运行应用以计划作业][img-schedulejobs]

## <a name="next-steps"></a>后续步骤
在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅[教程：如何更新固件][lnk-fwupdate]。

要了解如何将 AI 部署到具有 Azure IoT Edge 的边缘设备，请参阅 [IoT Edge 入门][lnk-iot-edge]。

<!-- images -->
[img-servicenuget]: ./media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: ./media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: ./media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: ./iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: ./iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: ./iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: ./iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ./iot-hub-linux-iot-edge-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/zh-cn/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

<!--Update_Description: update wording and code format-->