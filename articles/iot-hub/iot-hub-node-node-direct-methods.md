---
title: Azure IoT 中心直接方法 (Node) | Azure
description: 如何使用 Azure IoT 中心直接方法。 使用 Azure IoT SDK for Node.js 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 08/25/2017
ms.author: v-yiso
ms.custom: H1Hack27Feb2017
ms.date: 11/20/2017
ms.openlocfilehash: dbb058e961ecc9d04354fcb80eaefc59cd74e288
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52652781"
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>通过 Node.js 使用 IoT 设备上的直接方法
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

本教程结束时，会创建两个 Node.js 控制台应用：

* **CallMethodOnDevice.js**，用于在模拟设备应用上调用方法并显示响应。
* **SimulatedDevice.js**，可使用前面创建的设备标识连接到 IoT 中心，并响应通过云调用的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了 Azure IoT SDK，这些 SDK 可用于构建在设备和解决方案后端运行的应用程序。
> 
> 

要完成本教程，需要以下各项：

* Node.js 版本 4.0.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分，用户需创建一个 Node.js 控制台应用，用于响应通过云调用的方法。

1. 新建名为 **simulateddevice**的空文件夹。 在命令提示符下使用以下命令，在 **simulateddevice** 文件夹中创建一个 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **simulateddevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 设备 SDK 包和 **azure-iot-device-mqtt** 包：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 在 **SimulatedDevice.js** 文件夹中，利用文本编辑器创建新的 **simulateddevice** 文件。
4. 在 **SimulatedDevice.js** 文件的开头添加以下 `require` 语句：

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. 添加 **connectionString** 变量，并使用它创建 **DeviceClient** 实例。 将 **{device connection string}** 替换为在“创建设备标识”部分生成的设备连接字符串：

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. 添加以下函数，实现设备上的方法：

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. 打开与 IoT 中心的连接并开始初始化方法侦听器：

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. 保存并关闭 **SimulatedDevice.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如连接重试）。
> 
> 

## <a name="call-a-method-on-a-device"></a>调用设备上的方法
在此部分中，会创建一个 Node.js 控制台应用，该应用在模拟设备应用上调用方法并随后显示响应。

1. 新建名为 **callmethodondevice** 的空文件夹。 在 **callmethodondevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **callmethodondevice** 文件夹的命令提示符处，运行以下命令以安装 **azure-iothub** 包：
   
    ```
    npm install azure-iothub --save
    ```
3. 使用文本编辑器，在 **callmethodondevice** 文件夹中创建 **CallMethodOnDevice.js** 文件。
4. 在 **CallMethodOnDevice.js** 文件的开头添加以下 `require` 语句：

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```
5. 添加以下变量声明，并将占位符值替换为中心的 IoT 中心连接字符串：

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. 创建客户端，以便打开到 IoT 中心的连接。

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. 添加以下函数，以便调用设备方法并将设备响应输出到控制台：

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. 保存并关闭 **CallMethodOnDevice.js** 文件。

## <a name="run-the-apps"></a>运行应用
现在，已准备就绪，可以运行应用。

1. 在 **simulateddevice** 文件夹的命令提示符处运行以下命令，开始侦听从 IoT 中心发出的方法调用：

    ```
    node SimulatedDevice.js
    ```

    ![][7]
2. 在 **callmethodondevice** 文件夹的命令提示符处运行以下命令，开始监视 IoT 中心：

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
3. 此时会看到设备通过输出消息对方法进行响应，而调用该方法的应用程序则会显示来自设备的响应：

    ![][9]

## <a name="next-steps"></a>后续步骤
本教程中，在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已通过此设备标识启用模拟设备应用的相关功能，使之能够响应通过云调用的方法。 还创建了一个应用，用于调用设备上的方法并显示来自设备的响应。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [IoT 中心入门]
* [Schedule jobs on multiple devices（在多台设备上计划作业）][lnk-devguide-jobs]

若要了解如何扩展 IoT 解决方案并在多个设备上计划方法调用，请参阅 [Schedule and broadcast jobs][lnk-tutorial-jobs] （计划和广播作业）教程。

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/zh-cn/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: ./iot-hub-devguide-sdks.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/
[lnk-portal]: https://portal.azure.cn/

[lnk-devguide-jobs]: ./iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: ./iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: ./iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ./iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: ./iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: ./iot-hub-csharp-csharp-process-d2c.md
[IoT 中心入门]: ./iot-hub-node-node-getstarted.md