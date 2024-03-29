---
title: Azure Cosmos DB：SQL API 的 Java 示例
description: 在 GitHub 上查找使用 Azure Cosmos DB SQL API 的常见任务的 Java 示例，包括 CRUD 操作。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
origin.date: 02/08/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 0a997e5aaa2a3d2cfd0893571755d7560d0b3656
ms.sourcegitcommit: 153236e4ad63e57ab2ae6ff1d4ca8b83221e3a1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171372"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB：SQL API 的 Java 示例

> [!div class="op_single_selector"]
> * [.NET 示例](sql-api-dotnet-samples.md)
> * [Java 示例](sql-api-java-samples.md)
> * [异步 Java 示例](sql-api-async-java-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub 存储库中包含可对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的最新示例应用程序。 本文提供：

* 每个示例 Java项目文件中各项任务的链接。 
* 指向相关的 API 参考内容的链接。

**先决条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 可以[激活 Visual Studio 订阅者权益](https://www.azure.cn/support/legal/offer-rate-plans/)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

需要以下条件才能运行此示例应用程序：

* Java 开发工具包 7
* Azure DocumentDB Java SDK

可以选择使用 Maven 获取最新的 Azure Cosmos DB Java SDK 二进制文件，供在项目中使用。 Maven 会自动添加任何必需的依赖项。 否则，可以直接下载 pom.xml 文件中列出的依赖项并将它们添加到生成路径。

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**运行示例应用程序**

克隆示例存储库：
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

可以使用 Eclipse 或使用 Maven 从命令行运行示例。

通过 Eclipse 运行：
* 在 Eclipse 中加载主要上级项目 pom.xml 文件；它应该会自动加载 documentdb 示例。
* 若要运行这些示例，需要有效的 Azure Cosmos DB 终结点。 从 `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java` 读取终结点。
* 可以传递终结点凭据作为 Eclipse JUnit Run Config 中的参数，或将终结点凭据置于 AccountCredentials.java 中。
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.cn:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* 现在可以在 Eclipse 中运行这些示例，作为 JUnit 测试。

通过命令行运行：
* 运行示例的其他方式是使用 maven：
* 运行 Maven 并传递 Azure Cosmos DB 终结点凭据：
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.cn:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

    > [!NOTE]
    > 每个示例都是独立的，自行对自身进行设置并在完成后自行进行清理。 这些示例对 [DocumentClient.createCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) 发出多个调用。 每当执行此操作时，即会根据所创建的集合的性能层，对订阅收取使用 1 小时的费用。 
    > 
    > 

## <a name="database-examples"></a>数据库示例
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建和读取数据库](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [创建和删除数据库](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [创建和查询数据库](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>集合示例
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。

| 任务 | API 参考 |
| --- | --- |
| [创建单分区集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [创建自定义多分区集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [删除集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>文档示例
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。

| 任务 | API 参考 |
| --- | --- |
| [创建、读取和删除文档](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [使用可编程文档定义创建文档](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [文档](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>索引示例
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解如何在 Azure Cosmos DB 中进行索引编制，请参阅概念文章：[索引策略](index-policy.md)、[索引类型](index-types.md)和[索引路径](index-paths.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建索引并设置索引策略](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [索引](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="query-examples"></a>查询示例
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅概念文章：[SQL 查询示例](how-to-sql-query.md)。 

| 任务 | API 参考 |
| --- | --- |
| [执行简单的跨分区文档查询](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [按查询排序](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

有关编写查询的详细信息，请参阅 [Azure Cosmos DB 中的 SQL 查询](how-to-sql-query.md)。

## <a name="offer-examples"></a>优惠示例
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 文件演示如何执行以下任务：

| 任务 | API 参考 |
| --- | --- |
| [创建集合并设置吞吐量](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [读取集合以找到相关的优惠](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>分区键示例
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的分区和分区键，请参阅概念文章：[分区](partitioning-overview.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建单分区集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [更改单分区集合的吞吐量优惠](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>存储过程示例
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解如何在 Azure Cosmos DB 中进行服务器端编程，请参阅概念文章：[存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建存储过程](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [运行带参数的存储过程](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [运行带对象参数的存储过程](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |

<!-- Update_Description: update meta properties, update link -->
