---
title: Azure IoT 中心设备管理入门 (.NET/.NET) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备管理启动远程设备重启。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现包含直接方法的模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现调用直接方法的服务应用。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
origin.date: 09/15/2017
ms.author: v-jamebr
ms.date: 06/03/2019
ms.openlocfilehash: 1af0f0939a1a5a0c8f60f4177cf6ca2e7223bec7
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66194994"
---
# <a name="get-started-with-device-management-netnet"></a>设备管理入门 (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。
* 创建包含重新启动该设备的直接方法的模拟设备应用。 直接方法是从云中调用的。
* 创建一个 .NET 控制台应用，其通过 IoT 中心在模拟设备应用上调用重新启动直接方法。

在本教程结束时，会得到两个 .NET 控制台应用：

* **SimulateManagedDevice**，它使用先前创建的设备标识连接到 IoT 中心，接收重新启动直接方法，模拟物理重新启动，并报告上次重新启动的时间。
* **TriggerReboot**，它在模拟设备应用中调用直接方法、显示响应以及显示更新的报告属性。

要完成本教程，需要以下各项：

* Visual Studio。
* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动
在本部分中，你创建一个 .NET 控制台应用（使用 C#）以使用直接方法在设备上启动远程重新启动。 该应用使用设备孪生查询来搜索该设备的上次重新启动时间。

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到新解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **TriggerReboot**。

    ![新的 Visual C# Windows 经典桌面项目](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. 在“解决方案资源管理器”中，右键单击“TriggerReboot”项目，并单击“管理 NuGet 包”。

3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **Microsoft.Azure.Devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 包及其依赖项并添加对其的引用。

    ![“NuGet 包管理器”窗口](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在“创建 IoT 中心”部分中为中心创建的 IoT 中心连接字符串。 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. 将以下方法添加到 **Program** 类。  此代码获取重新启动设备的设备孪生并输出报告属性。
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. 将以下方法添加到 **Program** 类。  此代码使用直接方法在设备上发起重新启动操作。

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. 最后，在 **Main** 方法中添加以下行：
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. 生成解决方案。

> [!NOTE]
> 本教程仅针对设备的报告属性执行单个查询。 在生产代码中，建议使用轮询机制来检测报告属性的更改。

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

在本部分中执行以下操作：

* 创建一个 .NET 控制台应用，用于响应通过云调用的直接方法。

* 触发模拟设备重新启动。

* 通过报告的属性，设备孪生查询可标识设备及设备上次重新启动的时间。

1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为“SimulateManagedDevice”。
   
    ![新的 Visual C# Windows 经典设备应用](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. 在解决方案资源管理器中，右键单击“SimulateManagedDevice”项目，并单击“管理 NuGet 包...”。

3. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“Microsoft.Azure.Devices.Client”。 选择“安装”，安装“microsoft.azure.devices.client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依赖项并添加对其的引用。
   
    ![“NuGet 包管理器”窗口客户端应用](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分中记下的设备连接字符串。

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.cn;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. 添加以下函数，实现设备上的直接方法：

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并初始化方法侦听器：

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目...”。选择“单个启动项目”，并在下拉菜单中选择“SimulateManagedDevice”项目。 生成解决方案。       

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](https://msdn.microsoft.com/library/hh680901.aspx)（暂时性故障处理）中所述实施重试策略（例如指数退避）。


## <a name="run-the-apps"></a>运行应用
现在，已准备就绪，可以运行应用。

1. 若要运行 .NET 设备应用 **SimulateManagedDevice**， 右键单击“SimulateManagedDevice”项目，选择“调试”，并选择“启动新实例”。 它应开始侦听来自 IoT 中心的方法调用。 

2. 现在，设备已连接，正在等待方法调用，此时可运行 .NET **TriggerReboot** 应用来调用模拟设备应用中的重新启动方法。 为此，右键单击“TriggerReboot”项目，选择“调试”，并选择“启动新实例”。 应当会看到写入到  **SimulatedManagedDevice** 控制台中的“Rebooting!” 以及写入到 **TriggerReboot** 控制台中的设备报告属性（包括上次重新启动时间）。
   
    ![服务和设备应用运行](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: ./media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: ./media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: ./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://www.azure.cn/pricing/1rmb-trial/
[Azure portal]: https://portal.azure.cn/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/