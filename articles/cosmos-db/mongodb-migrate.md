---
title: 使用 mongoimport 和 mongorestore 将 MongoDB 数据迁移到 Azure Cosmos DB
description: 了解如何使用 mongoimport 和 mongorestore 将数据导入到 Cosmos DB。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
origin.date: 12/26/2018
ms.date: 01/21/2019
author: rockboyfor
ms.author: v-yeche
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: e70ef8bd38abb949d555beaba82737195fa2745b
ms.sourcegitcommit: 3577b2d12588826a674a61eb79bbbdfe5abe741a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54309293"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>将 MongoDB 数据迁移到 Azure Cosmos DB

 本教程说明如何将 MongoDB 中存储的数据，迁移到配置为使用 Cosmos DB 的用于 MongoDB 的 API 的 Azure Cosmos DB。 如果从 MongoDB 导入数据，并打算将其与 Azure Cosmos DB SQL API 配合使用，则应使用[数据迁移工具](import-data.md)来导入数据。

在本教程中，你将：

> [!div class="checklist"]
> * 准备迁移计划。
> * 使用 mongoimport 迁移数据。
> * 使用 mongorestore 迁移数据。

如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

开始迁移之前，请查看并满足以下先决条件：

### <a name="plan-for-the-migration"></a>规划迁移

本部分介绍如何规划数据迁移。 我们将估算 RU 费用、确定从计算机到云服务的延迟，并计算批大小和插入工作线程数。

#### <a name="pre-create-and-scale-your-collections"></a>预创建和缩放集合

使用 mongoimport 或 mongorestore 迁移之前，请通过 [Azure 门户](https://portal.azure.cn)或 MongoDB 驱动程序和工具预先创建所有集合。 

在 [Azure 门户](https://portal.azure.cn)中，提高用于迁移的集合吞吐量。 提高吞吐量后，可以避免受到速率限制，并缩短迁移时间。 可以在迁移后立即降低吞吐量，以节省成本。

除了在集合级别预配吞吐量以外，还可以针对一组集合在数据库级别预配吞吐量，以共享预配的吞吐量。 需要预先创建数据库和集合，并为共享吞吐量数据库中的每个集合定义分片键。

可以使用偏好的工具、驱动程序或 SDK 创建分片集合。 此示例使用 Mongo Shell 创建分片集合：

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```

该命令返回以下结果：

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>计算单文档写入的近似 RU 费用

从 MongoDB Shell 连接到配置为使用 Cosmos DB 的用于 MongoDB 的 API 的 Cosmos 帐户。 有关说明，请参阅[将 MongoDB 应用程序连接到 Cosmos DB](connect-mongodb-account.md)。

接下来，使用某个示例文档运行示例插入命令：

```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```

运行命令 `db.runCommand({getLastRequestStatistics: 1})`。

将返回以下输出所示的响应：

```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```

记下请求费用。

#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>确定从计算机到 Cosmos DB 的延迟

在 MongoDB Shell 中使用 `setVerboseShell(true)` 命令启用详细日志记录。

使用 `db.coll.find().limit(1)` 命令针对数据库运行基本查询。

将返回以下输出所示的响应：

```bash
Fetched 1 record(s) in 100(ms)
```

运行迁移之前，请删除已插入的文档，以确保没有重复的文档。 可以使用 `db.coll.remove({})` 命令删除文档。

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>计算 batchSize 和 numInsertionWorkers 属性的近似值

对于 **batchSize** 属性，请根据“确定从计算机到 Cosmos DB 的延迟”部分中的详述，将总预配吞吐量（RU/秒）数除单个文档写入所消耗的 RU 数。 如果计算出的小于或等于 24，请将该数字用作属性值。 如果计算出的值大于 24，请将属性值设置为 24。

对于 **numInsertionWorkers** 属性，请使用以下公式：

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

可以使用以下值来计算 **numInsertionWorkers** 属性的值：

| 属性 | 值 |
|--------|-----|
| **batchSize** | 24 |
| 预配的 RU 数 | 10,000 |
| 延迟 | 0.100 秒 |
| 消耗的 RU 数 | 10 RU |
| **numInsertionWorkers** | (10000 RU x 0.100 秒) / (24 x 10 RU) = **4.1666** |

运行 **monogoimport** 迁移命令。 本文稍后将介绍命令参数。

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.cn:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

也可以使用 **monogorestore** 命令。 确保所有集合的吞吐量都设置为前面计算中使用的 RU 数或更大。

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.cn:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>满足先决条件

规划迁移后，请完成以下步骤： 

* **获取示例数据**：开始迁移之前，请务必准备一些示例数据。 

* **提高吞吐量**：数据迁移的持续时间取决于为单个集合或数据库预配的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，请降低吞吐量以节省成本。 

* **启用 SSL**：Cosmos DB 遵守严格的安全要求和标准。 与 Cosmos 帐户交互时，请务必启用 SSL。 本文中的过程包括为 mongoimport 和 mongorestore 命令启用 SSL。

* **创建 Cosmos DB 资源**：开始迁移之前，请在 Azure 门户中预先创建所有集合。 若要迁移到具有数据库级预配吞吐量的 Cosmos 帐户，请务必在创建集合时提供分区键。

* **获取连接字符串**：在 [Azure 门户](https://portal.azure.cn)的左侧，选择“Azure Cosmos DB”条目。 在“订阅”下，选择自己的帐户名。 在“连接字符串”下，选择“连接字符串”。 门户右侧会显示连接到该帐户所需的信息：

    ![连接字符串信息](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>使用 mongoimport

若要将数据导入 Cosmos 帐户，请使用以下模板。

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

请将 \<your_hostname>、\<your_username> 和 \<your_password> 参数替换为帐户的特定值。 在以下示例中，我们使用 **sampleDB** 作为 \<your_database> 的值，使用 **sampleColl** 作为 \<your_collection> 的值：

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.cn:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>使用 mongorestore

若要将数据还原到使用 Cosmos DB 的用于 MongoDB 的 API 配置的 Cosmos 帐户，请使用以下模板执行导入。

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

请将 \<your_hostname>、\<your_username> 和 \<your_password> 参数替换为帐户的特定值。 在以下示例中，我们使用 **./dumps/dump-2016-12-07** 作为 \<path_to_backup> 的值：

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.cn:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的资源时，可以删除相应的资源组、Cosmos 帐户和所有相关资源。 使用以下步骤删除资源组：

1. 转到在其中创建了 Cosmos 帐户的资源组。
1. 选择“删除资源组”。
1. 确认要删除的资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇教程，了解如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 查询数据。 

> [!div class="nextstepaction"]
> [如何查询 MongoDB 数据](../cosmos-db/tutorial-query-mongodb.md)

<!--Update_Description: update meta properties, wording update -->