---
title: 了解 Azure IoT 中心标识注册表 | Azure
description: 开发人员指南 - 说明 IoT 中心标识注册表以及如何使用它来管理设备。 包括批量导入和导出设备标识的相关信息。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 08/29/2018
ms.author: v-yiso
ms.custom: H1Hack27Feb2017
ms.date: 10/29/2018
ms.openlocfilehash: 906fc1acfd556ee630e27fd355ce3e709ae18201
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674590"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>了解 IoT 中心的标识注册表

每个 IoT 中心都有一个标识注册表，存储允许连接到 IoT 中心的设备和模块的相关信息。 IoT 中心的标识注册表中必须先有设备或模块的条目，然后该设备或模块才能连接到 IoT 中心。 设备或模块还必须基于标识注册表中存储的凭据向 IoT 中心进行身份验证。

标识注册表中存储的设备或模块 ID 区分大小写。

概括地说，标识注册表是支持 REST 的设备或模块标识资源集合。 在此标识注册表中添加条目时，IoT 中心会创建一组每设备资源，如包含未送达云到设备消息的队列。

在需要时执行以下操作时可使用标识注册表：

* 设置连接到 IoT 中心的设备或模块。
* 控制每个设备/每个模块对中心的面向设备或模块的终结点的访问。

> [!NOTE]
> * 标识注册表不包含任何应用程序特定的元数据。
> * 模块标识和模块孪生为公共预览版。 下面的有关模块标识的功能在公开发布后会受支持。
>

## <a name="identity-registry-operations"></a>标识注册表操作
IoT 中心标识注册表公开以下操作：

* 创建设备或模块标识
* 更新设备或模块标识
* 按 ID 检索设备或模块标识
* 删除设备或模块标识
* 最多列出 1000 个标识
* 将设备标识导出到 Azure Blob 存储
* 将设备标识从 Azure Blob 存储导入

上述所有操作均可以使用 [RFC7232](https://tools.ietf.org/html/rfc7232) 中指定的乐观并发。

> [!IMPORTANT]
> 如果要检索 IoT 中心标识注册表中的所有标识，唯一方法是使用 [导出][lnk-export] 功能。
> 
> 

IoT 中心标识注册表：

* 不包含任何应用程序元数据。
* 可以将 **deviceId** 或 **moduleId** 作为键进行访问，就像字典一样。
* 不支持表达性查询。

IoT 解决方案通常具有不同的解决方案特定存储，其中包含应用程序特定的元数据。 例如，智能建筑物解决方案中的解决方案特定存储将记录部署温度感应器的房间信息。

> [!IMPORTANT]
> 只将标识注册表用于设备管理和预配操作。 运行时的高吞吐量操作不应依赖于在标识注册表中执行操作。 例如，在发送命令前先检查设备的连接状态就是不支持的模式。 请务必检查标识注册表的[限制速率][lnk-quotas]以及[设备检测信号][lnk-guidance-heartbeat]模式。
> 
> 

## <a name="disable-devices"></a>禁用设备
可以通过更新标识注册表中标识的 **状态** 属性来禁用设备。 通常在两种情况下使用此属性：

* 在预配协调过程中。 有关详细信息，请参阅 [Device Provisioning][lnk-guidance-provisioning]（设备预配）。
* 出于任何原因认为设备遭到入侵或未经授权。

此功能不适用于模块。

## <a name="import-and-export-device-identities"></a>导入和导出设备标识

可以使用 [IoT 中心资源提供程序终结点][lnk-endpoints]上的异步操作，从 IoT 中心的标识注册表批量导出设备标识。 导出是长时间运行的作业，它使用客户提供的 blob 容器来保存从标识注册表读取的设备标识数据。

可以使用 [IoT 中心资源提供程序终结点][lnk-endpoints]上的异步操作，向 IoT 中心的标识注册表批量导入设备标识。 导入是长时间运行的作业，它使用客户提供的 blob 容器中的数据，将设备标识数据写入标识注册表。

有关导入和导出 API 的详细信息，请参阅 [IoT 中心资源提供程序 REST API][lnk-resource-provider-apis]。 若要了解有关如何运行导入和导出作业的详细信息，请参阅 [Bulk management of IoT Hub device identities][lnk-bulk-identity]（批量管理 IoT 中心的设备标识）。

## <a name="device-provisioning"></a>Device Provisioning

给定的 IoT 解决方案存储的设备数据取决于该解决方案的特定要求。 但是，解决方案必须至少存储设备标识和身份验证密钥。 Azure IoT 中心包含标识注册表，可以存储每个设备的值，例如 ID、身份验证密钥和状态代码。 解决方案可以使用其他 Azure 服务（例如表存储、Blob 存储或 Cosmos DB）来存储任何其他设备数据。

*设备预配*是将初始设备数据添加到解决方案中存储中的过程。 要使新设备能够连接到中心，必须将设备 ID 和密钥添加到 IoT 中心的标识注册表。 在预配过程中，可能需要初始化其他解决方案存储中的设备特定数据。

## <a name="device-heartbeat"></a>检测信号

IoT 中心标识注册表包含名为 **connectionState**的字段。 开发和调试期间仅使用 **connectionState** 字段。 IoT 解决方案不应在运行时查询字段。 例如，不要在发送云到设备的消息或 SMS 之前查询 **connectionState** 字段以检查设备是否已连接。

如果 IoT 解决方案需要知道设备是否已连接，则可实现*检测信号模式*。
在检测信号模式下，设备每隔固定时间至少发送一次设备到云的消息（例如，每小时至少一次）。 因此，即使设备没有任何要发送的数据，仍会发送空的设备到云的消息（通常具有可供识别为检测信号的属性）。 在服务端，该解决方案维护着与每个设备收到的最后一个检测信号的映射。 如果解决方案未在预计时间内从设备收到检测信号消息，则它假定设备存在问题。

更复杂的实现可包含来自 [Azure Monitor][lnk-AM] 和 [Azure 资源运行状况][lnk-ARH]的信息，以便识别尝试连接或通信但失败的设备，请查阅[使用诊断进行监视][lnk-devguide-mon]指南。 实施检测信号模式时，请务必查看 [IoT 中心配额与限制][lnk-quotas]。

> [!NOTE]
> 如果 IoT 解决方案只使用连接状态来决定是否发送云到设备的消息，并且没有把消息广播到大量设备，则考虑使用更简单的较短到期时间模式。 此模式达到的效果与使用检测信号模式维护设备连接状态注册表达到的效果一样，而且更加有效。 如果请求消息确认，则 IoT 中心可以通知你哪些设备可以接收消息以及哪些设备不能接收。

## <a name="device-and-module-lifecycle-notifications"></a>设备和模块生命周期通知

创建或删除标识时，IoT 中心可通过发送生命周期通知来通知 IoT 解决方案。 为此，IoT 解决方案需要创建一个路由，并将“数据源”设置为等于 *DeviceLifecycleEvents* 或 *ModuleLifecycleEvents*。 默认情况下，不会发送生命周期通知，即无此类路由预先存在。 通知消息包括属性和正文。

属性：消息系统属性以 `$` 符号为前缀。

设备的通知消息：

| Name | 值 |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  发送通知的时间 |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** 或 **deleteDeviceIdentity** |
|hubName | IoT 中心的名称 |
|deviceId | 设备 ID |
|operationTimestamp | ISO8601 操作时间戳 |
|iothub-message-schema | deviceLifecycleNotification |

正文：本部分采用 JSON 格式，表示创建的设备孪生的标识。 例如，

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
模块的通知消息：

| Name | 值 |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  发送通知的时间 |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** 或 **deleteModuleIdentity** |
hubName | IoT 中心的名称 |
moduleId | 模块 的 ID |
operationTimestamp | ISO8601 操作时间戳 |
iothub-message-schema | moduleLifecycleNotification |

正文：本部分采用 JSON 格式，表示创建的模块孪生的标识。 例如，

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>设备标识属性
设备识别表示为包含以下属性的 JSON 文档：

| 属性 | 选项 | 说明 |
| --- | --- | --- |
| deviceId |必需，更新时只读 |ASCII 7 位字母数字字符 + 某些特殊字符（`- . + % _ # * ? ! ( ) , = @ $ '`）的区分大小写字符串（最长为 128 个字符）。 |
| generationId |必需，只读 |IoT 中心生成的区分大小写字符串，最长为 128 个字符。 在删除并重新创建设备时，此值用于区分具有相同 **deviceId**的设备。 |
| etag |必需，只读 |一个字符串，根据 [RFC7232](https://tools.ietf.org/html/rfc7232) 表示设备标识的弱 ETag。 |
| auth |可选 |包含身份验证信息和安全材料的复合对象。 |
| auth.symkey |可选 |包含主密钥和辅助密钥的复合对象，以 base64 格式存储。 |
| 状态 |必填 |访问指示器。 可以是 **Enabled** 或 **Disabled**。 如果是 **Enabled**，则允许设备连接。 如果是 **Disabled**，则此设备无法访问任何面向设备的终结点。 |
| statusReason |可选 |128 个字符的字符串，用于存储设备标识状态的原因。 允许所有 UTF-8 字符。 |
| statusUpdateTime |只读 |临时指示器，显示上次状态更新的日期和时间。 |
| connectionState |只读 |指示连接状态的字段：**Connected** 或 **Disconnected**。 此字段表示设备连接状态的 IoT 中心视图。 **重要说明**：此字段只用于开发/调试目的。 仅使用 MQTT 或 AMQP 的设备才更新连接状态。 此外，它基于协议级别的 ping（MQTT ping 或 AMQP ping），并且最多只有 5 分钟的延迟。 出于这些原因，可能会发生误报，例如将设备报告为已连接，但实际上已断开连接。 |
| connectionStateUpdatedTime |只读 |临时指示器，显示上次更新连接状态的日期和时间。 |
| lastActivityTime |只读 |临时指示器，显示设备上次连接、接收或发送消息的日期和时间。 |

> [!NOTE]
> 连接状态只能表示连接状态的 IoT 中心视图。 根据网络状态和配置，可能会延迟此状态的更新。

> [!NOTE]
> 目前，设备 SDK 不支持在 **deviceId** 中使用 `+` 和 `#` 字符。

## <a name="module-identity-properties"></a>模块标识属性

模块识别表示为包含以下属性的 JSON 文档：

| 属性 | 选项 | 说明 |
| --- | --- | --- |
| deviceId |必需，更新时只读 |ASCII 7 位字母数字字符 + 某些特殊字符（`- . + % _ # * ? ! ( ) , = @ $ '`）的区分大小写字符串（最长为 128 个字符）。 |
| moduleId |必需，更新时只读 |ASCII 7 位字母数字字符 + 某些特殊字符（`- . + % _ # * ? ! ( ) , = @ $ '`）的区分大小写字符串（最长为 128 个字符）。 |
| generationId |必需，只读 |IoT 中心生成的区分大小写字符串，最长为 128 个字符。 在删除并重新创建设备时，此值用于区分具有相同 **deviceId**的设备。 |
| etag |必需，只读 |一个字符串，根据 [RFC7232](https://tools.ietf.org/html/rfc7232) 表示设备标识的弱 ETag。 |
| auth |可选 |包含身份验证信息和安全材料的复合对象。 |
| auth.symkey |可选 |包含主密钥和辅助密钥的复合对象，以 base64 格式存储。 |
| 状态 |必填 |访问指示器。 可以是 **Enabled** 或 **Disabled**。 如果是 **Enabled**，则允许设备连接。 如果是 **Disabled**，则此设备无法访问任何面向设备的终结点。 |
| statusReason |可选 |128 个字符的字符串，用于存储设备标识状态的原因。 允许所有 UTF-8 字符。 |
| statusUpdateTime |只读 |临时指示器，显示上次状态更新的日期和时间。 |
| connectionState |只读 |指示连接状态的字段：**Connected** 或 **Disconnected**。 此字段表示设备连接状态的 IoT 中心视图。 **重要说明**：此字段只用于开发/调试目的。 仅使用 MQTT 或 AMQP 的设备才更新连接状态。 此外，它基于协议级别的 ping（MQTT ping 或 AMQP ping），并且最多只有 5 分钟的延迟。 出于这些原因，可能会发生误报，例如将设备报告为已连接，但实际上已断开连接。 |
| connectionStateUpdatedTime |只读 |临时指示器，显示上次更新连接状态的日期和时间。 |
| lastActivityTime |只读 |临时指示器，显示设备上次连接、接收或发送消息的日期和时间。 |

> [!NOTE]
> 目前，设备 SDK 不支持在 **deviceId** 和 **moduleId** 中使用 `+` 和 `#` 字符。

## <a name="additional-reference-material"></a>其他参考资料
IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-endpoints]，介绍了每个 IoT 中心针对运行时和管理操作公开的各种终结点。
* [限制和配额][lnk-quotas]介绍了适用于 IoT 中心服务的配额和限制行为。
* [Azure IoT 设备和服务 SDK][lnk-sdks]，列出了在开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。
* [IoT 中心查询语言][lnk-query]介绍了可用来从 IoT 中心检索设备克隆和作业相关信息的查询语言。
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt] 提供有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤

了解如何使用 IoT 中心标识注册表后，可以根据兴趣参阅以下 IoT 中心开发人员指南主题：

* [控制 IoT 中心的访问权限][lnk-devguide-security]
* [使用设备孪生同步状态和配置][lnk-devguide-device-twins]
* [在设备上调用直接方法][lnk-devguide-directmethods]
* [在多台设备上计划作业][lnk-devguide-jobs]

要尝试本文中介绍的一些概念，请参阅以下 IoT 中心教程：

* [Azure IoT 中心入门][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: ./iot-hub-devguide-endpoints.md
[lnk-quotas]: ./iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: ./iot-hub-devguide-sdks.md
[lnk-query]: ./iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: ./iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: ./iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: ./iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: ./iot-hub-bulk-identity-mgmt.md
[lnk-export]: ./iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-mon]: iot-hub-monitor-resource-health.md

[lnk-devguide-security]: ./iot-hub-devguide-security.md
[lnk-devguide-device-twins]: ./iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: ./iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: ./iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: quickstart-send-telemetry-dotnet.md
[lnk-AM]: /monitoring-and-diagnostics/
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-howto-evgrid-connstate]: iot-hub-how-to-order-connection-state-events.md
