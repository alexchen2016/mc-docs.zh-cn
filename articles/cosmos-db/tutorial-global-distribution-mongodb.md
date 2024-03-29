---
title: 使用 Azure Cosmos DB 的用于 MongoDB 的 API 进行多区域分发的教程
description: 了解如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 设置多区域分发。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
origin.date: 12/26/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 15f1be3173e1101106fb0234b9e168d9855cd7fd
ms.sourcegitcommit: 3577b2d12588826a674a61eb79bbbdfe5abe741a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54309082"
---
# <a name="set-up-multiple-region-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的用于 MongoDB 的 API 设置多区域分布式数据库

本文将展示如何使用 Azure 门户设置一个多区域分布式数据库，并使用 Azure Cosmos DB 的用于 MongoDB 的 API 连接到该数据库。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用 [Azure Cosmos DB 的用于 MongoDB 的 API](mongodb-introduction.md) 配置多区域分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>验证区域设置 
检查 Azure Cosmos DB 的用于 MongoDB 的 API 中的多区域配置的一种简单方法是从 Mongo Shell 运行 *isMaster()* 命令。

从 Mongo Shell：

   ```
      db.isMaster()
   ```

示例结果：

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "China North "
         },
         "hosts": [
            "vishi-api-for-mongodb-chinaeast.documents.azure.cn:10255",
            "vishi-api-for-mongodb-chinanorth.documents.azure.cn:10255",
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-chinanorth.documents.azure.cn:10255",
         "me": "vishi-api-for-mongodb-chinanorth.documents.azure.cn:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>连接到首选区域 

使用 Azure Cosmos DB 的用于 MongoDB 的 API，可以为多区域分布式数据库指定集合的读取首选项。 为实现低延迟读取和多区域高可用性，建议将集合的读取首选项设置为“就近”。 当读取首选项配置为“就近”时，将从最近的区域进行读取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

对于具有主读取/写入区域和用于灾难恢复 (DR) 方案的辅助区域的应用程序，建议将集合的读取首选项设置为“辅助优先”。 当读取首选项配置为“辅助优先”时，如果主区域不可用，将从辅助区域进行读取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最后，如果愿意，可以手动指定读取区域。 可以在读取首选项内设置区域标记。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "China East");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理多区域复制帐户的一致性。 有关 Azure Cosmos DB 中多区域数据库复制工作原理的详细信息，请参阅[使用 Cosmos DB 多区域分配数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中已完成以下操作：

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用 Cosmos DB 的用于 MongoDB 的 API 配置多区域分发

现可继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 模拟器在本地进行开发](local-emulator.md)

<!--Update_Description: update meta properties,wording update-->