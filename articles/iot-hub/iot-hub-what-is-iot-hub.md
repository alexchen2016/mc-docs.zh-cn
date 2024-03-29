---
title: Azure IoT 中心概述 | Azure
description: Azure IoT 中心服务概述：什么是 IoT 中心、设备连接、物联网通信模式、网关和服务辅助通信模式
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 01/29/2018
ms.author: v-yiso
ms.custom: H1Hack27Feb2017
ms.date: 03/12/2018
ms.openlocfilehash: ef2cbead67092fa530cfac9432c3a6f3f2852e15
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627700"
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Azure IoT 中心服务概述
欢迎使用 Azure IoT 中心。 本文概述 Azure IoT 中心，并描述应该使用此服务实现物联网 (IoT) 解决方案的原因。 Azure IoT 中心是一项完全托管的服务，可在数百万个 IoT 设备和一个解决方案后端之间实现安全可靠的双向通信。 Azure IoT 中心：

* 提供了多个设备到云和云到设备通信选项。 这些选项包括单向消息传送、文件传输和请求-回复方法。
* 提供路由到其他 Azure 服务的内置声明性消息。
* 提供设备元数据和同步状态信息的可查询存储。
* 使用每个设备的安全密钥或 X.509 证书来实现安全通信和访问控制。
* 可广泛监视设备连接性和设备标识管理事件。
* 包含最流行语言和平台的设备库。

[IoT 中心与事件中心的比较][lnk-compare] 一文描述这两个服务之间的主要差异，并重点说明了在 IoT 解决方案中使用 IoT 中心的优点。

有关 Azure 和 IoT 中心如何帮助保护 IoT 解决方案的详细信息，请参阅[物联网安全基础知识][lnk-security-ground-up]。

![在物联网解决方案中充当云网关的 Azure IoT 中心][img-architecture]

> [!NOTE]
> 有关 IoT 体系结构的深入讨论，请参阅 [Azure IoT Reference Architecture][lnk-refarch]（Azure IoT 参考体系结构）。
> 
> 

## <a name="iot-device-connectivity-challenges"></a>IoT 设备连接性挑战
IoT 中心和设备库可帮助应对挑战，即如何以可靠且安全的方式将设备连接到解决方案后端。 IoT 设备：

- 通常是无人操作的嵌入式系统。
- 可位于物理访问较昂贵的远程位置。
- 可能只能通过解决方案后端来访问。
- 能力和处理资源可能都有限。
- 网络连接可能不稳定、缓慢或昂贵。
- 可能需要使用专属、自定义或行业特定的应用程序协议。
- 可以使用大量常见的硬件和软件平台来创建。

除了上述需求之外，所有 IoT 解决方案还必须提供可扩展性、安全性和可靠性。 使用传统技术（例如 Web 容器和消息传送代理）时，所产生的一系列连接需求不仅难以实现，而且实现起来非常耗时。

## <a name="why-use-azure-iot-hub"></a>为何使用 Azure IoT 中心？

Azure IoT 中心提供了一组丰富的[设备到云][lnk-d2c-guidance]和[云到设备][lnk-c2d-guidance]通信选项。 另外，Azure IoT 中心还可以应对通过以下方法可靠且安全地连接到设备时所面临的挑战：

* **设备孪生**。 可以使用[设备孪生][lnk-twins]存储、同步和查询设备元数据和状态信息。 设备孪生是存储设备状态信息（例如元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到 IoT 中心的每台设备维护一个设备孪生。

* **每个设备的身份验证和安全连接性**。 可以为每个设备设置独有的 [安全密钥][lnk-devguide-security]，让它连接到 IoT 中心。 [IoT 中心标识注册表][lnk-devguide-identityregistry] 会在解决方案中存储设备标识和密钥。 解决方案后端可将单个设备添加到允许或拒绝列表，以便完全掌控设备访问权限。

* **基于声明性规则，将设备到云消息路由到 Azure 服务**。 IoT 中心根据路由规则定义消息路由，用于控制中心发送设备到云消息的位置。 路由规则不要求用户编写任何代码，并且可以代替自定义的引入后消息调度程序。

* **将 IoT 中心事件集成到商业应用程序中**。 IoT 中心与 Azure 事件网格集成。 使用此集成来配置其他 Azure 服务或第三方应用程序，以便侦听 IoT 中心事件。 使用 Azure 事件网格可以采用可靠、可缩放且安全的方式快速响应关键事件。

* **设备连接操作监视**。 可以收到有关设备标识管理操作与设备连接事件的详细操作日志。 此监视功能使得 IoT 解决方案能够查明连接问题。 可以使用这些日志查明提供了错误凭据、过于频繁地发送消息或者拒绝了所有云到设备消息的设备。

* **一组丰富的设备库**。 [Azure IoT 设备 SDK][lnk-device-sdks] 适用于各种语言和平台 - C 面向很多 Linux 分发版、Windows 和实时操作系统。 Azure IoT 设备 SDK 也支持 C#、Java 和 JavaScript 等托管语言。

* **IoT 协议和可扩展性**。 如果解决方案无法使用设备库，则 IoT 中心会公开一个公共协议，它使设备可以通过本机方式使用 MQTT v3.1.1、HTTPS 1.1 或 AMQP 1.0 协议。 还可以通过以下方式扩展 IoT 中心来支持自定义的协议：

  * 使用 [Azure IoT Edge][lnk-iot-edge] 创建现场网关，以便将自定义协议转换为 IoT 中心理解的协议。
  * 自定义 [Azure IoT 协议网关][protocol-gateway]（在云中运行的一个开放源代码组件）。

* **扩展**。 Azure IoT 中心可扩展为数百万个同时连接的设备，以及每秒数百万个事件。

## <a name="gateways"></a>网关

IoT 解决方案中的网关通常是部署在云中的[协议网关][lnk-iotedge]或者是在设备本地部署的[现场网关][lnk-field-gateway]。 协议网关执行协议转换，例如 MQTT 到 AMQP。 现场网关可在边缘运行分析、制定能减少延迟的时间敏感型决策，提供设备管理服务，强制实施安全和隐私约束，还可执行协议转换。 这两种网关都可以充当设备与 IoT 中心之间的媒介。

现场网关与简单的流量路由设备（例如网络地址转换设备或防火墙）不同，因为它通常在解决方案中管理访问和信息流中扮演主动的角色。

解决方案可以同时包含协议网关和现场网关。

## <a name="how-does-iot-hub-work"></a>IoT 中心如何运作？

Azure IoT 中心会实现 [服务辅助通信][lnk-service-assisted-pattern] 模式，以调节设备与解决方案后端之间的交互。 此模式的目的是在控制系统（例如 IoT 中心）与专用设备（位于不受信任的物理空间中）之间，建立可信任的双向通信路径。 该模式会建立下列原则：

- 安全性的优先级高于其他所有功能。
- 设备不接受未经请求的网络信息。 设备以仅限出站的方式建立所有连接和路由。 若要让设备从解决方案后端接收命令，设备必须定期启动连接，检查是否有任何挂起的命令要处理。
- 设备只能同与它们对等的已知服务（例如 IoT 中心）进行连接或建立路由。
- 设备和服务之间或网关之间的通信路径在应用程序协议层受到保护。
- 系统级别的授权和身份验证以每个设备的标识为基础。 它们可让访问凭据和权限近乎实时地撤销。
- 对于因为电源或连接性问题而偶尔进行连接的设备，可以保留命令和通知，直到设备进行连接并接收它们，从而实现双向通信。 IoT 中心会为发送的命令维护设备特定的队列。
- 针对通过网关到特定服务的受保护传输，应用程序有效负载数据会受到单独保护。

移动行业已使用服务辅助通信模式来实现推送通知服务，例如 [Windows 推送通知服务][lnk-wns]。

支持通过 ExpressRoute 的公共对等路径访问 IoT 中心。

## <a name="next-steps"></a>后续步骤

若要开始编写一些代码并运行一些示例，请参阅 [《IoT 中心入门》][lnk-get-started] 教程。

若要了解如何从设备发送消息并从 IoT 中心接收消息以及如何配置消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-send-messages]。

若要了解 IoT 中心如何实现标准的设备管理，从而远程管理、配置和更新设备，请参阅 [IoT 中心设备管理概述][lnk-device-management]。

若要在各种设备硬件平台和操作系统上实现客户端应用程序，可使用 Azure IoT 设备 SDK。 设备 SDK 包含一些库，有助于将遥测数据发送到 IoT 中心和接收云到设备的消息。 使用设备 SDK 时，可从选取各种网络协议与 IoT 中心进行通信。 若要了解详细信息，请参阅 [设备 SDK 的相关信息][lnk-device-sdks]。

[img-architecture]: ./media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: ./iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服务辅助通信，博客作者 Clemens Vasters"
[lnk-compare]: ./iot-hub-compare-event-hubs.md
[lnk-iotedge]: ./iot-hub-protocol-gateway.md
[lnk-field-gateway]: ./iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: ./iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/zh-cn/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-send-messages]: ./iot-hub-devguide-messaging.md
[lnk-device-management]: ./iot-hub-device-management-overview.md

[lnk-twins]: ./iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: ./iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: ./iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: ./iot-hub-security-ground-up.md

<!--Update_Description:update meta properties and wording-->