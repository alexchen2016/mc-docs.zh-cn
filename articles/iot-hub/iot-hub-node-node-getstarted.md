---
title: Azure IoT 中心入门 (Node) | Azure
description: 了解如何通过用于 Node.js 的 IoT SDK 将设备到云消息发送到 Azure IoT 中心。 创建模拟的设备和服务应用，以便通过 IoT 中心注册设备、发送消息和读取消息。
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 08/31/2017
ms.date: 12/18/2017
ms.author: v-yiso
ms.openlocfilehash: c383a4e4a663e78d0f6fd7ccc1f403162fe85261
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626098"
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>使用 Node 将模拟设备连接到 IoT 中心
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教程结束时，将拥有 3 个 Node.js 控制台应用：

* **CreateDeviceIdentity.js**，它创建用于连接模拟设备应用的设备标识和关联的安全密钥。
* **ReadDeviceToCloudMessages.js**，它显示模拟设备应用发送的遥测数据。
* **SimulatedDevice.js**，它使用前面创建的设备标识连接到 IoT 中心，并使用 MQTT 协议每秒发送一次遥测消息。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了 Azure IoT SDK，这些 SDK 可用于构建在设备和解决方案后端运行的应用程序。

要完成本教程，需要以下各项：

* Node.js 版本 4.0.x 或更高版本。

* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

现在已创建 IoT 中心。 已获得完成本教程的其余部分所需的 IoT 中心主机名和 IoT 中心连接字符串。

## <a name="create-a-device-identity"></a>创建设备标识

本部分创建一个 Node.js 控制台应用，用于在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 **IoT 中心开发人员指南** 的 [标识注册表][lnk-devguide-identity]部分。 运行此应用，生成的唯一设备 ID 和密钥可供设备在发送设备到云的消息时用来标识自我。

1. 新建名为 `createdeviceidentity` 的空文件夹。 在 `createdeviceidentity` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：

    ```cmd/sh
    npm init
    ```

2. 在 `createdeviceidentity` 文件夹中的命令提示符处，使用以下命令安装 `azure-iothub` 服务 SDK 包：

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 使用文本编辑器，在 `createdeviceidentity` 文件夹中创建一个 CreateDeviceIdentity.js 文件。

4. 在 **CreateDeviceIdentity.js** 文件的开头添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. 向 CreateDeviceIdentity.js 文件添加以下代码。 将占位符值替换为在上一部分为中心创建的 IoT 中心连接字符串：

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. 添加以下代码，在 IoT 中心的设备标识注册表中创建设备定义。 如果标识注册表中没有设备 ID，此代码会创建一个设备；否则它返回现有设备的密钥：

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. 保存并关闭 **CreateDeviceIdentity.js** 文件。

8. 若要运行 `createdeviceidentity` 应用程序，请在 `createdeviceidentity` 文件夹中的命令提示符处执行以下命令：

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```
9. 记下**设备 ID** 和**设备密钥**。 稍后在创建连接到作为设备的 IoT 中心的应用程序时需要这些值。

> [!NOTE]
> IoT 中心标识注册表仅存储用于实现 IoT 中心安全访问的设备标识。 它存储设备 ID 和密钥作为安全凭据，以及启用或禁用标志（可用于禁用对单个设备的访问）。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。
> 
> 

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>接收设备到云的消息

本部分创建一个 Node.js 控制台应用，用于读取来自 IoT 中心的设备到云消息。 IoT 中心公开与[事件中心][lnk-event-hubs-overview]兼容的终结点，以便你可读取设备到云的消息。 为了简单起见，本教程创建的基本读取器不适用于高吞吐量部署。 [Process device-to-cloud messages][lnk-process-d2c-tutorial] （处理设备到云的消息）教程介绍了如何大规模处理设备到云的消息。 [事件中心入门][lnk-eventhubs-tutorial]教程提供的详细信息适用于与 IoT 中心事件中心兼容的终结点。

> [!NOTE]
> 与事件中心兼容的终结点始终使用 AMQP 协议读取设备到云的消息。
> 
> 

1. 创建名为 `readdevicetocloudmessages` 的空文件夹。 在 `readdevicetocloudmessages` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：

    ```cmd/sh
    npm init
    ```

2. 在 `readdevicetocloudmessages` 文件夹中的命令提示符处，使用以下命令安装  azure-event-hubs 包：

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. 使用文本编辑器，在 `readdevicetocloudmessages` 文件夹中创建一个 **ReadDeviceToCloudMessages.js** 文件。

4. 在 **ReadDeviceToCloudMessages.js** 文件的开头添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. 添加以下变量声明，并将占位符值替换为中心的 IoT 中心连接字符串：

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```
6. 添加以下两个函数用于在控制台中列显输出：

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. 添加以下代码以创建 **EventHubClient**，打开与 IoT 中心的连接，并为每个分区创建接收器。 在创建开始运行后只读取发送到 IoT 中心的消息的接收方时，此应用程序会使用筛选器。 此筛选器仅显示当前的消息集，很适合测试环境。 在生产环境中，代码需保证处理所有消息。 有关详细信息，请参阅 [如何处理 IoT 中心设备到云的消息][lnk-process-d2c-tutorial] 教程：

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. 保存并关闭 **ReadDeviceToCloudMessages.js** 文件。

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
本部分创建一个 Node.js 控制台应用，用于模拟向 IoT 中心发送设备到云消息的设备。

1. 创建名为 `simulateddevice` 的空文件夹。 在 `simulateddevice` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：

    ```cmd/sh
    npm init
    ```

2. 在 `simulateddevice` 文件夹的命令提示符处，运行下述命令以安装 azure-iot-device 设备 SDK 包和 azure-iot-device-mqtt 包：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 在 `simulateddevice` 文件夹中，利用文本编辑器创建 SimulatedDevice.js 文件。

4. 在 **SimulatedDevice.js** 文件的开头添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 添加 `connectionString` 变量，并使用它创建一个客户端实例。 将 `{youriothostname}` 替换为在“创建 IoT 中心”部分创建的 IoT 中心的名称。 将 `{yourdevicekey}` 替换为在“创建设备标识”部分生成的设备密钥值：

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```
6. 添加以下函数以显示应用程序的输出：

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. 创建回调，并使用 **setInterval** 函数每秒向 IoT 中心发送一次消息：

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
8. 与 IoT 中心建立连接并开始发送消息：

    ```nodejs
    client.open(connectCallback);
    ```
9. 保存并关闭 **SimulatedDevice.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 
> 

## <a name="run-the-apps"></a>运行应用
现在，已准备就绪，可以运行应用。

1. 在 `readdevicetocloudmessages` 文件夹中的命令提示符处运行以下命令，开始监视 IoT 中心：

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![用于监视设备到云消息的 Node.js IoT 中心服务应用][7]

2. 在 `simulateddevice` 文件夹中的命令提示符处运行以下命令，开始将遥测数据发送到 IoT 中心：

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![用于发送设备到云消息的 Node.js IoT 中心设备应用][8]
3. [Azure 门户][lnk-portal]中的“使用情况”磁贴显示发送到 IoT 中心的消息数：

    ![显示发送到 IoT 中心的消息数的 Azure 门户“使用情况”磁贴][43]

## <a name="next-steps"></a>后续步骤
本教程中，在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已使用此设备标识来让模拟设备应用向 IoT 中心发送设备到云的消息。 还创建了一个应用，用于显示 IoT 中心接收的消息。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [连接设备][lnk-connect-device]
* [设备管理入门][lnk-device-management]
* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iot-edge]

若要了解如何扩展 IoT 解决方案和如何大规模处理设备到云的消息，请参阅 [Process device-to-cloud messages][lnk-process-d2c-tutorial] （处理设备到云的消息）教程。
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ./iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: ./iot-hub-devguide-sdks.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/
[lnk-portal]: https://portal.azure.cn/

<!--Update_Description:update meta properties and code block language specification-->

[lnk-device-management]: ./iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ./iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://www.azure.cn/develop/iot/