---
title: Azure IoT 中心设备孪生入门 (.NET/.NET) | Azure
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现可添加标记并运行 IoT 中心查询的服务应用。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
origin.date: 05/15/2017
ms.author: v-yiso
ms.date: 06/03/2019
ms.openlocfilehash: e74d5490b4751fbe789c796f5ae5d08f17931345
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195017"
---
# <a name="get-started-with-device-twins-netnet"></a>设备孪生入门 (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，将会创建这些 .NET 控制台应用：

* CreateDeviceIdentity，一个 .NET 应用，用于创建设备标识和关联的安全密钥以连接模拟设备应用。
* AddTagsAndQuery，一个 .NET 后端应用，用于添加标记并查询设备孪生。
* ReportConnectivity，一个 .NET 设备应用，它模拟使用早前创建的设备标识连接到 IoT 中心的设备，并报告其连接条件。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

若要完成本教程，需要满足以下条件：

* Visual Studio。
* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>创建服务应用
本部分创建一个 .NET 控制台应用（使用 C#），该应用将位置元数据添加到与 **myDeviceId** 关联的设备孪生。 然后，该应用选择位于美国的设备来查询存储在 IoT 中心的设备孪生，然后查询报告手机网络连接的设备孪生。

1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **AddTagsAndQuery**。
   
    ![新的 Visual C# Windows 经典桌面项目](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. 在“解决方案资源管理器”中，右键单击“AddTagsAndQuery”项目，并单击“管理 NuGet 包...”。

3. 在“NuGet 包管理器”窗口中，选择“浏览”，并搜索“Microsoft.Azure.Devices”。 选择“安装”以安装“Microsoft.Azure.Devices”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 包及其依赖项并添加对其的引用。
   
    ![“NuGet 包管理器”窗口](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. 将以下字段添加到 Program 类。 将占位符值替换为在上一部分为中心创建的 IoT 中心连接字符串。

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. 将以下方法添加到 **Program** 类：

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    **RegistryManager** 类公开从该服务与设备孪生交互所需的所有方法。 上面的代码首先初始化 **registryManager** 对象，并检索 **myDeviceId** 的设备孪生，最后使用所需位置信息更新其标记。
   
    在更新后，它执行两个查询：第一个仅选择位于 **Redmond43** 工厂的设备的设备孪生，第二个将查询细化为仅选择还要通过移动电话网络连接的设备。
   
    请注意上面的代码，当它创建 **query** 对象时，会指定返回的最大文档数。 **query** 对象包含 **HasMoreResults** 布尔值属性，可以使用它多次调用 **GetNextAsTwinAsync** 方法来检索所有结果。 名为 **GetNextAsJson** 的方法可用于非设备孪生的结果（例如聚合查询的结果）。

7. 最后，在 **Main** 方法中添加以下行：

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. 在“解决方案资源管理器”中，打开“设置启动项目...”，并确保 **AddTagsAndQuery** 项目的“操作”为“启动”。 生成解决方案。

9. 右键单击 **AddTagsAndQuery** 项目并选择“调试”，并选择“启动新实例”来运行此应用程序。 在查询位于 **Redmond43** 的所有设备的查询结果中，应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。
   
    ![在窗口中查询结果](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

在下一部分中，创建的设备应用会报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用
在此部分，需创建一个 .NET 控制台应用作为“myDeviceId”连接到中心，然后更新其报告属性，使其包含它使用手机网络进行连接的信息。

1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为“ReportConnectivity”。
   
    ![新建 Visual C# Windows 经典设备应用](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. 在解决方案资源管理器中，右键单击“ReportConnectivity”项目，然后单击“管理 NuGet 包...”。

3. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“Microsoft.Azure.Devices.Client”。 选择“安装”，安装“microsoft.azure.devices.client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依赖项并添加对其的引用。
   
    ![“NuGet 包管理器”窗口客户端应用](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分中记下的设备连接字符串。

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Client** 对象公开从该设备与设备孪生交互所需的所有方法。 上述代码会初始化“客户端”对象，然后检索 myDeviceId设备孪生。

7. 将以下方法添加到 **Program** 类：

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");
            
            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   上述代码会使用连接信息更新 myDeviceId 的报告属性。

8. 最后，在 **Main** 方法中添加以下行：

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

9. 在“解决方案资源管理器”中，打开“设置启动项目...”，并确保“ReportConnectivity”项目的“操作”为“启动”。 生成解决方案。

10. 右键单击“ReportConnectivity”项目并选择“调试”，然后选择“启动新实例”来运行此应用程序。 应该可以看到应用获取孪生信息，然后发送连接信息作为报告属性。
   
    ![运行设备应用来报告连接信息](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. 现在设备报告了其连接信息，该信息应出现在两个查询中。 运行 .NET **AddTagsAndQuery** 应用即可再次运行查询。 这一次 **myDeviceId** 应显示在两个查询结果中。
   
    ![成功报告设备连接信息](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>后续步骤
本教程中，在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备孪生中的设备连接信息。 还学习了如何使用类似 SQL 的 IoT 中心查询语言来查询此信息。

充分利用以下资源：

* 按照[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-dotnet.md)教程从设备发送遥测数据，
* 按照[使用所需属性配置设备](tutorial-device-twins.md)教程使用设备孪生的所需属性配置设备，
* 按照[使用直接方法](quickstart-control-device-dotnet.md)教程以交互方式控制设备（例如从用户控制的应用打开风扇）。

<!-- images -->
[img-servicenuget]: ./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: ./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: ./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: ./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: ./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: ./iot-hub-devguide-sdks.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: ./iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: ./iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ./iot-hub-devguide-direct-methods.md
[lnk-twins]: ./iot-hub-devguide-device-twins.md
[lnk-query]: ./iot-hub-devguide-query-language.md
[lnk-identity]: ./iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-how-to-configure]: tutorial-device-twins.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

