---
title: Azure 事件网格传送和重试
description: 介绍 Azure 事件网格如何传送事件以及如何处理未送达的消息。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
origin.date: 01/01/2019
ms.date: 06/03/2019
ms.author: v-yiso
ms.openlocfilehash: f59e15b67c3de77fb9979be0296fcad74f5b40c0
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195244"
---
# <a name="event-grid-message-delivery-and-retry"></a>事件网格消息传送和重试

本文介绍了未确认送达时 Azure 事件网格如何处理事件。

事件网格提供持久传送。 它会将每个订阅的每条消息至少发送一次。 事件会立即发送到每个订阅的已注册终结点。 如果终结点未确认收到事件，事件网格会重试传送事件。

目前，事件网格单独将每个事件发送到订阅者。 订阅者接收包含单个事件的数组。

## <a name="retry-schedule-and-duration"></a>重试计划和持续时间

对于事件传送，事件网格使用指数性的回退重试策略。 如果终结点未响应或返回失败代码，事件网格会按照以下计划尽量重新尝试传送：

1. 10 秒
1. 30 秒
1. 1 分钟
1. 5 分钟
1. 10 分钟
1. 30 分钟
1. 1 小时
1. 按小时论的话，最多 24 小时

事件网格为所有重试步骤添加小的随机性，在某个终结点持续运行不正常、停机很长时间，或者看起来已过载的情况下，会适时跳过某些重试。

对于确定性行为，请在[订阅重试策略](manage-event-delivery.md)中设置事件生存时间和最大传递尝试次数。

默认情况下，事件网格会使所有在 24 小时内未送达的事件过期。 创建事件订阅时，可[自定义重试策略](manage-event-delivery.md)。 提供最大传递尝试次数（默认值为 30）和事件生存时间（默认为 1440 分钟）。

## <a name="dead-letter-events"></a>死信事件

当事件网格无法传递事件时，它会将未送达的事件发送到某个存储帐户。 此过程称为死信处理。 默认情况下，事件网格不启用死信处理。 若要启用该功能，在创建事件订阅时必须指定一个存储帐户来存放未送达的事件。 你将从此存储帐户中拉取事件来解决传递问题。

事件网格已进行所有重试尝试后会将事件发送到死信位置。 如果事件网格收到 400（错误请求）或 413（请求实体太大）响应代码，它会立即将事件发送到死信终结点。 这些响应代码指示事件传送将永远不会成功。

最后一次尝试发送事件与发送到死信位置之间有五分钟的延迟。 此延迟旨在减少 Blob 存储操作的数量。 如果死信位置已四小时不可用，则会丢弃该事件。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建事件订阅时，需要提供此容器的终结点。 终结点的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

你可能希望在事件发送到死信位置时收到通知。 每当死信 blob 存储收到未送达的事件时，事件网格都会通知处理程序。 处理程序使用你希望采取的、用于协调未送达的事件的操作进行响应。

有关设置死信位置的示例，请参阅[死信和重试策略](manage-event-delivery.md)。

## <a name="message-delivery-status"></a>消息传送状态

事件网格使用 HTTP 响应代码确认已接收事件。 

### <a name="success-codes"></a>成功代码

以下 HTTP 响应代码表示事件已成功发送至 webhook。 事件网格会视为传送已完成。

- 200 正常
- 202 已接受

### <a name="failure-codes"></a>失败代码

以下 HTTP 响应代码表示事件传送尝试失败。

- 400 错误的请求
- 401 未授权
- 404 未找到
- 408 请求超时
- 413 请求实体太大
- 414 URI 太长
- 429 请求次数过多
- 500 内部服务器错误
- 503 服务不可用
- 504 网关超时

## <a name="next-steps"></a>后续步骤

* 若要查看事件传送的状态，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 若要自定义事件传送选项，请参阅[死信和重试策略](manage-event-delivery.md)。