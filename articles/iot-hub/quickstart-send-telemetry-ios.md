---
title: 快速入门：向 Azure IoT 中心发送遥测数据 | Microsoft Docs
description: 在本快速入门中，请运行一个示例 iOS 应用程序，以便向 IoT 中心发送模拟遥测数据，以及从 IoT 中心读取需在云中处理的遥测数据。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
origin.date: 04/20/2018
ms.date: 12/03/2018
ms.author: v-yiso
ms.openlocfilehash: 076757fc4717d9c2cd9e84d70cb89e92b334adc9
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675112"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>快速入门：将遥测数据从设备发送到 IoT 中心 (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 在本文中，请将遥测数据从模拟设备应用程序发送到 IoT 中心， 然后即可从后端应用程序查看数据。 

本文使用预先编写的 Swift 应用程序来发送遥测数据，使用 CLI 实用程序从 IoT 中心读取遥测数据。 


如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

- 从 [Azure 示例](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)下载代码示例 
- 最新版本的 [XCode](https://developer.apple.com/xcode/)，运行最新版本的 iOS SDK。 本快速入门已使用 XCode 9.3 和 iOS 11.3 测试过。
- 最新版 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，请使用 Azure CLI 来注册模拟设备。

1. 运行以下命令，以添加 IoT 中心 CLI 扩展并创建设备标识。 

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **myiOSdevice**：这是为注册的设备提供的名称。 请按显示的方法使用 myiOSdevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

   ```azurecli
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name YourIoTHubName --device-id myiOSdevice
   ```

1. 运行以下命令，获取刚注册设备的设备连接字符串：

   ```azurecli
   az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id myiOSdevice --output table
   ```

   记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.cn;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

示例应用程序在 iOS 设备上运行，该设备连接到 IoT 中心的特定于设备的终结点，并发送模拟的温度和湿度遥测数据。 

### <a name="install-cocoapods"></a>安装 CocoaPods

CocoaPods 管理那些使用第三方库的 iOS 项目的依赖项。

在本地终端窗口中，导航到在先决条件部分下载的 Azure-IoT-Samples-iOS 文件夹。 然后，导航到示例项目：

```sh
cd quickstart/sample-device
```

确保 XCode 已关闭，运行以下命令，以便安装在 **podfile** 文件中声明的 CocoaPods：

```sh
pod install
```

除了为项目安装所需的 Pod，安装命令还创建了一个 XCode 工作区文件，该文件已配置为对依赖项使用 Pod。 

### <a name="run-the-sample-application"></a>运行示例应用程序 

1. 在 XCode 中打开示例工作区。

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. 展开“MQTT 客户端示例”项目，然后展开同一名称的文件夹。  
3. 打开 **ViewController.swift**，以便在 XCode 中进行编辑。 
4. 搜索 **connectionString** 变量，并使用以前记下的设备连接字符串更新其值。
5. 保存所做更改。 
6. 使用“生成并运行”按钮或“Command + R”组合键在设备模拟器中运行项目。 

   ![运行项目](media/quickstart-send-telemetry-ios/run-sample.png)

7. 当模拟器打开后，在示例应用中选择“启动”。

以下屏幕截图显示了在应用程序将模拟遥测数据发送到 IoT 中心后的一些示例输出：

   ![运行模拟设备](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

在 XCode 模拟器上运行过的示例应用显示从设备发送的消息的相关数据。 也可通过 IoT 中心查看接收的数据。 IoT 中心 CLI 扩展可以连接到 IoT 中心上的服务端**事件**终结点。 扩展会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

运行以下命令，并将 `YourIoTHubName` 替换为 IoT 中心的名称：

```azurecli
az iot hub monitor-events --device-id myiOSdevice --hub-name YourIoTHubName
```

以下屏幕截图显示了扩展接收到模拟设备发送到中心的遥测数据时的输出：

以下屏幕截图显示在本地终端窗口中看到的遥测数据的类型：

![查看遥测数据](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本文中，你设置了 IoT 中心、注册了设备、将模拟遥测数据从 iOS 设备发送到了中心，并从中心读取了遥测数据。 

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/quickstart-linux.md
[lnk-connect-device]: /develop/iot/
