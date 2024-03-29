---
title: Azure IoT 中心设备孪生入门 (Node) | Azure
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用 Azure IoT SDK for Node.js 实现模拟设备应用，并实现可添加标记和运行 IoT 中心查询的服务应用。
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
origin.date: 08/25/2017
ms.date: 06/03/2018
ms.author: v-yiso
ms.openlocfilehash: 240e0eaec31711d4905996020ea50d4970079571
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66194981"
---
# <a name="get-started-with-device-twins-node"></a>设备孪生入门 (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，你将拥有两个 Node.js 控制台应用：

* **AddTagsAndQuery.js**（Node.js 后端应用），用于添加标记和查询设备孪生。
*  TwinSimulatedDevice.js（Node.js 应用），用于模拟使用早先创建的设备标识连接到 IoT 中心的设备，并报告其连接状况。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

若要完成本教程，需要满足以下条件：

* Node.js 版本 10.0.x 或更高版本。

* 有效的 Azure 帐户。 如果没有帐户，可以创建一个[试用帐户][lnk-free-trial]，只需几分钟即可完成。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>创建服务应用
在此部分，会创建一个 Node.js 控制台应用，将位置元数据添加到与 **myDeviceId**关联的设备孪生。 然后，该应用将选择位于美国的设备来查询存储在 IoT 中心的设备孪生，然后查询报告移动电话网络连接的设备孪生。

1. 新建名为 **addtagsandqueryapp**的空文件夹。 在命令提示符下的**addtagsandqueryapp** 文件夹中，使用以下命令创建新的 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **addtagsandqueryapp** 文件夹的命令提示符下，运行以下命令以安装 **azure-iothub** 包：
   
    ```
    npm install azure-iothub --save
    ```
3. 使用文本编辑器，在 **addtagsandqueryapp** 文件夹中创建一个新的 **AddTagsAndQuery.js** 文件。
4. 将以下代码添加到 **AddTagsAndQuery.js** 文件，并将 **{iot hub connection string}** 占位符替换为创建中心时复制的 IoT 中心连接字符串：

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'CN',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Registry** 对象公开从服务与设备孪生进行交互所需的所有方法。 前面的代码首先初始化 **Registry** 对象，然后检索 **myDeviceId** 的设备孪生，最后使用所需位置信息更新其标记。
   
    更新标记后，它将调用 queryTwins 函数  。
5. 在 **AddTagsAndQuery.js** 末尾添加以下代码以实现 **queryTwins** 函数：

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    前面的代码执行两个查询：第一个只选择位于 **Redmond43** 工厂中的设备的设备孪生，第二个会优化查询以只选择还通过手机网络连接的设备。
   
    上面的代码创建 query 对象时，会指定返回的最大文档数  。 **query** 对象包含 **hasMoreResults** 布尔值属性，可以使用它多次调用 **nextAsTwin** 方法来检索所有结果。 名为 next 的方法可用于非设备孪生的结果（例如聚合查询的结果）  。
6. 使用以下内容运行应用程序：
   
        node AddTagsAndQuery.js
   
    在查询位于 **Redmond43** 的所有设备的查询结果中，应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。

    ![][1]

在下一部分中，创建的设备应用会报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用
在此部分，会创建一个 Node.js 控制台应用作为 **myDeviceId**连接到中心，并更新其设备孪生的报告属性，说明它是使用手机网络进行连接的。


1. 新建名为 **reportconnectivity**的空文件夹。 在 **reportconnectivity** 文件夹的命令提示符处，使用以下命令创建新的 package.json 文件。 接受所有默认值：

    ```
    npm init
    ```
2. 在 **reportconnectivity** 文件夹中，在命令提示符下运行以下命令以安装 **azure-iot-device** 包和 **azure-iot-device-mqtt** 包：

    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文本编辑器，在 **reportconnectivity** 文件夹中创建一个新的 **ReportConnectivity.js** 文件。
4. 将以下代码添加到 **ReportConnectivity.js** 文件，并将 **{device connection string}** 占位符替换为创建 **myDeviceId** 设备标识时复制的设备连接字符串：
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** 对象公开从该设备与设备孪生交互所需的所有方法。 上面的代码在初始化 **Client** 对象后会检索 **myDeviceId** 的设备孪生，并使用连接信息更新其报告属性。
5. 运行设备应用
   
        node ReportConnectivity.js
   
    此时会显示消息 `twin state reported`。
6. 现在设备报告了其连接信息，应出现在两个查询中。 返回到 **addtagsandqueryapp** 文件夹，再次运行查询：
   
        node AddTagsAndQuery.js
   
    这次 **myDeviceId** 应出现在两个查询结果中。

    ![][3]

## <a name="next-steps"></a>后续步骤
本教程中，在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备孪生中的设备连接信息。 还学习了如何使用类似 SQL 的 IoT 中心查询语言来查询此信息。

充分利用以下资源：

* 通过 [Get started with IoT Hub][lnk-iothub-getstarted] （IoT 中心入门）教程学习如何从设备发送遥测；
* 通过[使用所需属性配置设备][lnk-twin-how-to-configure]教程学习如何使用设备孪生的所需属性配置设备；
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。


<!-- images -->
[1]: ./media/iot-hub-node-node-twin-getstarted/service1.png
[3]: ./media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: ./iot-hub-devguide-sdks.md
[lnk-free-trial]: https://www.azure.cn/pricing/1rmb-trial/

[lnk-d2c]: ./iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ./iot-hub-devguide-direct-methods.md
[lnk-twins]: ./iot-hub-devguide-device-twins.md
[lnk-query]: ./iot-hub-devguide-query-language.md
[lnk-identity]: ./iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-node.md
[lnk-device-management]: ./iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/quickstart-linux.md
[lnk-connect-device]: https://www.azure.cn/develop/iot/

[lnk-twin-how-to-configure]: tutorial-device-twins.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-mqtt]: ./iot-hub-mqtt-support.md