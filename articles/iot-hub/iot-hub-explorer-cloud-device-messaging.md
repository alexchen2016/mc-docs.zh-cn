---
title: 使用 iothub-explorer 管理 Azure IoT 中心云设备消息传送 | Azure
description: 了解如何在 Azure IoT 中心内使用 iothub-explorer CLI 工具监视设备到云 (D2C) 的消息以及发送到云到设备 (C2D) 的消息。
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: iothub explorer, 云设备消息传送, iot 中心云到设备, 云到设备的消息
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 04/11/2018
ms.author: v-yiso
ms.date: 05/07/2018
ms.openlocfilehash: bf9c97b54cba7e118d09a56324c748a5aae553ff
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52651376"
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用 iothub-explorer 在设备与 IoT 中心之间发送和接收消息

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) 提供一些命令用于简化 IoT 中心的管理。 本教程重点介绍如何使用 iothub-explorer 在设备与 IoT 中心之间发送和接收消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>要学习的知识

了解如何使用 iothub-explorer 监视设备到云的消息以及发送云到设备的消息。 设备到云的消息可能是设备收集的，随后要发送到 IoT 中心的传感器数据。 云到设备的消息可能是 IoT 中心发送到设备的，用于闪烁连接到设备的 LED 的命令。

## <a name="what-you-will-do"></a>执行的操作

- 使用 iothub-explorer 监视设备到云的消息。
- 使用 iothub-explorer 发送云到设备的消息。

## <a name="what-you-need"></a>需要什么

- 已完成教程[设置设备](./iot-hub-raspberry-pi-kit-node-get-started.md)，其中涵盖以下要求：
  - 一个有效的 Azure 订阅。
  - 已在订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的客户端应用程序。
- iothub-explorer。 （[安装 iothub-explorer](https://github.com/azure/iothub-explorer)）

## <a name="monitor-device-to-cloud-messages"></a>监视设备到云的消息

若要监视设备发送到 IoT 中心的消息，请执行以下步骤：

1. 打开控制台窗口。
1. 运行以下命令：

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > 从 IoT 中心获取 `<device-id>` 和 `<IoTHubConnectionString>`。 确保已完成以前的教程。 或者可以尝试使用 `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"`（如果有 `HostName`、`SharedAccessKeyName` 和 `SharedAccessKey`）。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

要将消息从 IoT 中心发送到设备，请执行以下步骤：

1. 打开控制台窗口。
1. 运行以下命令，在 IoT 中心内启动一个会话：

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. 运行以下命令，将消息发送到设备：

   ```bash
   iothub-explorer send <device-id> <message>
   ```

该命令将闪烁连接到设备的 LED，并将消息发送到设备。

> [!Note]
> 设备收到消息后，不需要向 IoT 中心发送单独的确认命令。

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


<!--Update_Description: update meta data only-->