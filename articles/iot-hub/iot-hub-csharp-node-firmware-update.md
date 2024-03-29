---
title: 通过 Azure IoT 中心进行设备固件更新 (.NET/Node) | Azure
description: 如何使用 Azure IoT 中心上的设备管理进行设备固件更新。 使用适用于 Node.js 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现可触发固件更新的服务应用。
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 08/30/2017
ms.author: v-yiso
ms.date: 06/11/2018
ms.openlocfilehash: c8c3eb4b8576923d2cd7b74d0601b073ab8d19f0
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648628"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>使用设备管理启动设备固件更新 (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

在[设备管理入门][lnk-dm-getstarted]教程中，已了解如何使用[设备孪生][lnk-devtwin]和[直接方法][lnk-c2dmethod]基元来远程重新启动设备。 本教程使用相同的 IoT 中心基元，并演示如何进行端到端模拟固件更新。  此模式在用于 [Raspberry Pi device implementation sample][lnk-rpi-implementation]（Raspberry Pi 设备实现示例）的固件更新实现中使用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个 .NET 控制台应用，其通过 IoT 中心在模拟设备应用上调用 firmwareUpdate 直接方法。
* 创建模拟设备应用，以便实现 **firmwareUpdate** 直接方法。 该方法会启动等待下载固件映像、下载固件映像以及最后应用固件映像的多阶段过程。 在更新的每个阶段中，设备使用报告属性来报告进度。

本教程结束时，用户会有一个 Node.js 控制台设备应用，以及一个 .NET (C#) 控制台后端应用：

**dmpatterns_fwupdate_service.js**，它调用模拟设备应用中的直接方法、显示响应并定期（每隔 500 毫秒）显示更新的报告属性。

**TriggerFWUpdate**，它使用早前创建的设备标识连接到 IoT 中心，接收 firmwareUpdate 直接方法，运行一个多状态过程以模拟固件更新，此过程包括：等待映像下载、下载新映像以及最后应用映像。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 版本 4.0.x 或更高版本； <br/>  [准备开发环境][lnk-dev-setup]介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。
* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

按照[设备管理入门](./iot-hub-csharp-node-device-management-get-started.md)一文创建 IoT 中心，并获取 IoT 中心连接字符串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程固件更新
在本部分中，用户创建一个 .NET 控制台应用（使用 C#），以便在设备上启动远程固件更新。 该应用使用直接方法来启动更新，并使用设备孪生查询定期获取活动固件更新的状态。

1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **TriggerFWUpdate**。

    ![新的 Visual C# Windows 经典桌面项目][img-createapp]

2. 在“解决方案资源管理器”中，右键单击“TriggerFWUpdate”项目，并单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 此过程会下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口][img-servicenuget]
1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. 将以下字段添加到 **Program** 类。 将多个占位符值替换为在上一部分中为中心创建的 IoT 中心连接字符串和设备 ID。
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. 将以下方法添加到 **Program** 类：
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. 将以下方法添加到 **Program** 类：

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. 最后，在 **Main** 方法中添加以下行：
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. 在“解决方案资源管理器”中，打开“设置启动项目...”，并确保 **TriggerFWUpdate** 项目的“操作”为“启动”。

8. 生成解决方案。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>运行应用
现在，已准备就绪，可以运行应用。

1. 在 **manageddevice** 文件夹的命令提示符处，运行以下命令以开始侦听重新启动直接方法。

    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 在 Visual Studio 中，右键单击“TriggerFWUpdate”项目，依次选择“调试”和“启动新实例”。

3. 可在控制台查看对直接方法的设备响应。

    ![已成功更新固件][img-fwupdate]

## <a name="next-steps"></a>后续步骤
在本教程中，直接触发了设备的远程固件更新，并使用了报告属性跟踪固件更新的进度。

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- images -->
[img-servicenuget]: ./media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: ./media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: ./media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: ./iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ./iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: ./iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: ./iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/zh-cn/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/