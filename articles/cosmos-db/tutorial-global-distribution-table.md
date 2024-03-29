---
title: 表 API 的 Azure Cosmos DB 多区域分发教程
description: 了解如何使用表 API 设置 Azure Cosmos DB 多区域分发。
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
origin.date: 12/13/2017
ms.date: 03/18/2019
ms.reviewer: sngun
ms.openlocfilehash: 0effbde2188820bf63b8262bca5dfd9e96c65983
ms.sourcegitcommit: 66e360fe2577c9b7ddd96ff78e0ede36c3593b99
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "57988337"
---
<!--Verify sucessfully-->
# <a name="set-up-azure-cosmos-db-multiple-region-distribution-using-the-table-api"></a>使用表 API 设置 Azure Cosmos DB 多区域分发

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用[表 API](table-introduction.md) 配置多区域分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用表 API 连接到首选区域

为了利用[多区域分发](distribute-data-globally.md)，客户端应用程序可以指定要用于执行文档操作的区域优先顺序列表。 这可以通过设置 [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations) 属性来完成。 Azure Cosmos DB 表 API SDK 将基于帐户配置、当前区域可用性和所提供的首选项列表选取要与之通信的最佳终结点。

PreferredLocations 应包含以逗号分隔的首选（多宿主）位置列表，以供读取使用。 每个客户端实例可按首选顺序指定这些区域的一个子集以实现低延迟的读取。 必须使用这些区域的[显示名称](https://msdn.microsoft.com/library/azure/gg441293.aspx)命名这些区域，例如 `China North`。

将所有读取请求发送到 PreferredLocations 列表中的第一个可用区域。 如果请求失败，客户端会将请求转发到列表中的下一个区域，依此类推。

SDK 将尝试读取 PreferredLocations 中指定的区域。 例如，如果数据库帐户在三个区域中可用，但客户端只为 PreferredLocations 指定了两个非写入区域，那么，即使是在故障转移时，也不会从写入区域为读取提供服务。

SDK 自动将所有写入请求发送到当前写入区域。

如果未设置 PreferredLocations 属性，则会从当前写入区域为所有请求提供服务。

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理多区域复制帐户的一致性。 有关 Azure Cosmos DB 中多区域数据库复制工作原理的详细信息，请参阅[使用 Cosmos DB 多区域分配数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中已完成以下操作：

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用 Azure Cosmos DB 表 API 配置多区域分发

<!--Update_Description: new articles on turorial global distribution table -->
<!--ms.date: 03/18/2019-->