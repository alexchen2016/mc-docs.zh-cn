---
title: Azure Cosmos DB 表 API 简介
description: 了解如何通过 Azure Tables API 使用 Azure Cosmos DB 以低延迟存储和查询大量键值对数据。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
origin.date: 11/20/2017
ms.date: 03/18/2019
ms.author: v-yeche
ms.openlocfilehash: f8da574bde4584314212f51d43551d1e75766388
ms.sourcegitcommit: 66e360fe2577c9b7ddd96ff78e0ede36c3593b99
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "57988517"
---
<!--Verify sucessfully-->
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 简介：表 API

[Azure Cosmos DB](introduction.md) 提供的表 API 适用于为 Azure 表存储编写且需要以下高级功能的应用程序：

* [统包式多区域分布](distribute-data-globally.md)。
* 中国的[专用吞吐量](partition-data.md)。
* 99% 的情况下低至个位数的毫秒级延迟。
* 保证高可用性。
* [自动编制辅助索引](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)。

为 Azure 表存储编写的应用程序无需更改代码便可使用表 API 迁移到 Azure Cosmos DB，并可充分利用高级功能。 表 API 包含可用于 .NET、Java、Python 和 Node.js 的客户端 SDK。

## <a name="table-offerings"></a>表产品/服务
如果当前使用 Azure 表存储，可以通过移到 Azure Cosmos DB 表 API 获得以下好处：

| | Azure 表存储 | Azure Cosmos DB 表 API |
| --- | --- | --- |
| Latency | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在中国任何位置，对于任何规模，99% 的情况下读取操作的延迟 < 10 毫秒、写入操作的延迟 < 15 毫秒） |
| 吞吐量 | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒。 |
| 多区域分布 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 不能启动故障转移。 | [统包式多区域分布](distribute-data-globally.md)，至少分布到 1 个区域，最多分布到 30 多个区域。 支持在中国各地随时[自动和手动故障转移](high-availability.md)。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 自动对所有属性完成编制索引，没有索引管理。 |
| Query | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 存储优化。 | 吞吐量优化。 |
| SLA | 99.99% 可用性。 | 在公开发布时，为所有单区域帐户和具有松散一致性的所有多区域帐户提供 99.99% 的可用性 SLA，为所有多区域数据库帐户提供 99.999% 的读取可用性方面的[行业领先的综合 SLA](https://www.azure.cn/support/sla/cosmos-db/)。 |

## <a name="get-started"></a>入门

在 [Azure 门户](https://portal.azure.cn)中创建 Azure Cosmos DB 帐户。 然后开始使用我们的[通过 .NET 使用表 API 的快速入门](create-table-dotnet.md)。 

> [!IMPORTANT]
> 如果已在预览期间创建表 API 帐户，请[新建表 API 帐户](create-table-dotnet.md#create-a-database-account)，这样才能使用正式版表 API SDK。
>

## <a name="next-steps"></a>后续步骤

下面是一些可帮助你入门的指南：
* [使用表 API 生成 .NET 应用程序](create-table-dotnet.md)
* [在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)
* [使用表 API 查询表数据](tutorial-query-table.md)
* [了解如何使用表 API 设置 Azure Cosmos DB 多区域分发](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 表 .NET API](table-sdk-dotnet.md)
* [Azure Cosmos DB 表 Java API](table-sdk-java.md)
* [Azure Cosmos DB 表 Node.js API](table-sdk-nodejs.md)
* [用于 Python 的 Azure Cosmos DB 表 SDK](table-sdk-python.md)

<!--Update_Description: new articles on table introduction -->
<!--ms.date: 03/18/2019-->