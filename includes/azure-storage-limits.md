---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 01/11/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 991bc11b0ef16e2cbc8ad6eaa42bdd3526dbad6e
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64860121"
---
下表介绍 Azure 常规用途 v1、v2 和 Blob 存储帐户的默认限制。 “传入”限制是指请求中发送到存储帐户的所有数据。 “传出”限制是指响应中从存储帐户接收的所有数据。

| Resource | 默认限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括标准帐户和高级帐户 | 250 |
| 最大存储帐户容量 |  500 TB |
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口<sup>1</sup> | 如果已启用 RA-GRS/GRS，则为 5 Gbps；对于 LRS，为 10 Gbps<sup>2</sup> |
| 常规用途 v2 存储帐户和 Blob 存储帐户的最大出口 | 50 Gbps |
| 常规用途 v1 存储帐户的最大出口 | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS，为 15 Gbps<sup>2</sup> |

<sup>1</sup> Azure 标准存储帐户根据请求支持更高的入口上限。 若要请求提高帐户入口上限，请与 [Azure 支持](https://www.azure.cn/support/contact/)联系。

<sup>2</sup>[Azure 存储复制](/storage/common/storage-redundancy)选项包括：
* **RA-GRS**：读取访问异地冗余存储。 如果已启用 RA-GRS，辅助位置的出口目标与主要位置的出口目标相同。
* **GRS**：异地冗余存储。 
* **LRS**：本地冗余存储。 

> [!NOTE]
> 大多数情况下，建议使用常规用途 v2 存储帐户。 可以轻松将常规用途 v1 或 Azure Blob 存储帐户升级到常规用途 v2 帐户，无需停机且无需复制数据。
>
> 有关 Azure 存储帐户的详细信息，请参阅[存储帐户概述](../articles/storage/common/storage-account-overview.md)。 

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅 [Azure 存储定价](https://azure.cn/pricing/details/storage/)。

所有存储帐户都在扁平网络拓扑上运行，无论它们创建于何时，都支持本文所述的可伸缩性和性能目标。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Azure 存储：具有高度一致性的高可用云存储服务](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

