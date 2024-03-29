---
title: 在 Azure Cosmos DB 中预配数据库吞吐量
description: 了解如何在 Azure Cosmos DB 中预配数据库级别的吞吐量
author: rockboyfor
ms.service: cosmos-db
ms.topic: sample
origin.date: 05/23/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 29655afa4d6512e7c0b63866b0e98eb49ae7255a
ms.sourcegitcommit: 43eb6282d454a14a9eca1dfed11ed34adb963bd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151397"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中的数据库上预配吞吐量

本文介绍如何在 Azure Cosmos DB 中的数据库上预配吞吐量。 可以为单个[容器](how-to-provision-container-throughput.md)预配吞吐量，也可以为数据库预配吞吐量，并在数据库中的容器之间共享吞吐量。 若要了解何时使用容器级和数据库级吞吐量，请参阅[在容器和数据库上预配吞吐量的用例](set-throughput.md)一文。 可以使用 Azure 门户或 Azure Cosmos DB SDK 来预配数据库级别吞吐量。

## <a name="provision-throughput-using-azure-portal"></a>使用 Azure 门户预配吞吐量

<a name="portal-sql"></a>
### <a name="sql-core-api"></a>SQL（核心）API

1. 登录到 [Azure 门户](https://portal.azure.cn/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建数据库”   。 提供以下详细信息：

    * 输入数据库 ID。 
    * 选择“预配吞吐量”。 
    * 输入吞吐量（例如 1000 RU）。
    * 选择“确定”  。

    ![“新建数据库”对话框屏幕截图](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>使用 .NET SDK 预配吞吐量

> [!Note]
> 使用适用于 SQL API 的 Cosmos SDK 为所有 API 预配吞吐量。 也可以选择将以下示例用于 Cassandra API。

<a name="dotnet-all"></a>
### <a name="all-apis"></a>所有 API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

<a name="dotnet-cassandra"></a>
### <a name="cassandra-api"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解在 Azure Cosmos DB 中预配的吞吐量：

* [全局缩放预配的吞吐量](scaling-throughput.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [如何为容器预配吞吐量](how-to-provision-container-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)

<!-- Update_Description: update meta properties, wording update -->
