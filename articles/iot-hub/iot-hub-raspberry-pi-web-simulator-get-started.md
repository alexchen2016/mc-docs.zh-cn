---
title: 连接到云的模拟 Raspberry Pi (Node.js) - 将 Raspberry Pi Web 模拟器连接到 Azure IoT 中心 | Microsoft Docs
description: 将 Raspberry Pi Web 模拟器连接到 Azure IoT 中心，以供 Raspberry Pi 将数据发送到 Azure 云。
author: rangv
manager: ''
keywords: raspberry pi 模拟器, azure iot raspberry Pi, raspberry pi iot 中心, raspberry pi 将数据发送到云, 连接到云的 raspberry pi
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 04/11/2018
ms.author: v-yiso
ms.date: 05/06/2019
ms.openlocfilehash: 2204679a21e5343946b388c468ef733a6085e93c
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855179"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>将 Raspberry Pi 联机模拟器连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，首先学习有关使用 Raspberry Pi 联机模拟器的基础知识。 然后将学习如何使用 [Azure IoT 中心](about-iot-hub.md)将 Pi 模拟器无缝连接到云。 

如有物理设备，请访问[将 Raspberry Pi 连接到 Azure IoT 中心](./iot-hub-raspberry-pi-kit-node-get-started.md)。 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>准备工作

* 了解 Raspberry Pi 联机模拟器的基础知识。
* 创建 IoT 中心。
* 在 IoT 中心内为 Pi 注册设备。
* 在 Pi 上运行示例应用程序，将模拟传感器数据发送到 IoT 中心。

将模拟 Raspberry Pi 连接到所创建的 IoT 中心。 然后使用模拟器运行示例应用程序，生成传感器数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。 如果没有 Azure 帐户，只需花费几分钟就能[创建一个 Azure 试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。
* 如何使用 Raspberry Pi 联机模拟器。
* 如何将传感器数据发送到 IoT 中心。

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi Web 模拟器概述

单击用于启动 Raspberry Pi 联机模拟器的按钮。

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">启动 Raspberry Pi 模拟器</a>

Web 模拟器中有三个区域。
1. 装配区 - 默认电路是 Pi 与 BME280 传感器和 LED 连接。 在预览版中该区域是锁定的，因此当前无法执行自定义操作。
2. 编码区 - 一个联机代码编辑器，用于使用 Raspberry Pi 进行编码。 默认示例应用程序有助于从 BME280 传感器收集传感器数据，并发送到 Azure IoT 中心。 应用程序与实际 Pi 设备是完全兼容的。 
3. 集成控制台窗口 - 会显示代码的输出。 此窗口顶部有三个按钮。
   * 运行 - 在编码区中运行应用程序。
   * 重置 - 将编码区重置为默认示例应用程序。
   * 折叠/展开 - 按钮位于右侧，用于折叠/展开控制台窗口。

> [!NOTE]
> Raspberry Pi Web 模拟器现于预览版中提供。 我们希望通过 [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator) 听到你的建议。 [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator) 上公开了源代码。

![Pi 联机模拟器概述](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>在 Pi Web 模拟器上运行示例应用程序

1. 在编码区中，请确保正在使用默认示例应用程序。 将 15 行中的占位符替换为 Azure IoT 中心设备的连接字符串。
   ![替换设备连接字符串](./media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. 选择“运行”，或键入 `npm start`，即可运行应用程序。

应看到以下输出，其中显示传感器数据以及发送至 IoT 中心的消息![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>读取 IoT 中心收到的消息

若要监视 IoT 中心从模拟设备收到的消息，一种方法是使用适用于 Visual Studio Code 的 Azure IoT Tools。 若要了解详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure IoT Tools 在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

若要了解如何通过更多方式来处理设备发送的数据，请转到下一部分。

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!--Update_Description:update meta properties and wording-->