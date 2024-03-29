---
title: Azure Cosmos DB：SQL API 的 .NET 示例
description: 在 GitHub 上查找使用 Azure Cosmos DB SQL API 的常见任务的 C# .NET 示例，包括 CRUD 操作。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
origin.date: 04/04/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 5449344f38a87908d6702cad1e4baed6d272d876
ms.sourcegitcommit: 153236e4ad63e57ab2ae6ff1d4ca8b83221e3a1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171364"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB：SQL API 的 .NET 示例
> [!div class="op_single_selector"]
> * [.NET 示例](sql-api-dotnet-samples.md)
> * [Java 示例](sql-api-java-samples.md)
> * [异步 Java 示例](sql-api-async-java-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) GitHub 存储库中包含可对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的最新 .NET 示例解决方案。 本文提供：

* 指向每个示例 C# 项目文件中各项任务的链接。 
* 指向相关的 API 参考内容的链接。

有关 .NET SDK 3.0 版（预览版）的代码示例，请参阅 [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3) GitHub 存储库中的最新示例。 

## <a name="prerequisites"></a>先决条件

已安装包含 Azure 开发工作流的 Visual Studio 2019
- 你可以下载并使用**免费的** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。  

[Microsoft.Azure.DocumentDB NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Azure 订阅，或免费的 Cosmos DB 试用帐户
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- 可以[激活 Visual Studio 订阅者权益](https://www.azure.cn/support/legal/offer-rate-plans/)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> 这些示例都是独立的，运行完成后，可通过多次调用 [CreateDocumentCollectionAsync()](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync?view=azure-dotnet) 对其进行设置和清理。 每次执行此操作时，都会根据集合的性能层，对订阅收取使用 1 小时的费用。 
> 

## <a name="database-examples"></a>数据库示例
*DatabaseManagement* 项目示例的 [RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L72-L121) 方法演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建数据库](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L90) |[DocumentClient.CreateDatabaseAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseasync?view=azure-dotnet) |
| [Query a database](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L81) |[DocumentQueryable.CreateDatabaseQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdatabasequery.aspx) |
| [按 ID 读取数据库](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L102) |[DocumentClient.ReadDatabaseAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync?view=azure-dotnet) |
| [Read all the databases](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L108-L113) |[DocumentClient.ReadDatabaseFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync?view=azure-dotnet) |
| [删除数据库](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L118) |[DocumentClient.DeleteDatabaseAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync?view=azure-dotnet) |

## <a name="collection-examples"></a>集合示例
*CollectionManagement* 项目示例的 [RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/CollectionManagement/Program.cs#L96-L185) 方法演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建集合](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L101) |[DocumentClient.CreateDocumentCollectionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync?view=azure-dotnet) |
| [Get configured performance of a collection](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L198) |[DocumentQueryable.CreateOfferQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) |
| [Change configured performance of a collection](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L207) |[DocumentClient.ReplaceOfferAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) |
| [按 ID 获取集合](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L153) |[DocumentClient.ReadDocumentCollectionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync?view=azure-dotnet) |
| [Read all the collections in a database](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L162) |[DocumentClient.ReadDocumentCollectionFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync?view=azure-dotnet) |
| [删除集合](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L175) |[DocumentClient.DeleteDocumentCollectionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync?view=azure-dotnet) |

## <a name="document-examples"></a>文档示例
*DocumentManagement* 项目示例的 [RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L97-L102) 方法演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L198) |[DocumentClient.CreateDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync?view=azure-dotnet) |
| [按 ID 读取文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L211) |[DocumentClient.ReadDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync?view=azure-dotnet) |
| [Read all the documents in a collection](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L227) |[DocumentClient.ReadDocumentFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet) |
| [查询文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L248-L251) |[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [替换文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L263) |[DocumentClient.ReplaceDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync?view=azure-dotnet) |
| [更新插入文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L300) |[DocumentClient.UpsertDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync?view=azure-dotnet) |
| [删除文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L322) |[DocumentClient.DeleteDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync?view=azure-dotnet) |
| [使用 .NET 动态对象](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L331-L380) |[DocumentClient.CreateDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync?view=azure-dotnet)<br />[DocumentClient.ReadDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync?view=azure-dotnet)<br />[DocumentClient.ReplaceDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync?view=azure-dotnet) |
| [使用条件 ETag 检查替换文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L398-L440) |[DocumentClient.AccessCondition](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.accesscondition?view=azure-dotnet)<br />[Documents.Client.AccessConditionType](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.accessconditiontype?view=azure-dotnet) |
| [仅当文档已更改时读取文档](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L442-L470) |[DocumentClient.AccessCondition](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.accesscondition?view=azure-dotnet)<br />[Documents.Client.AccessConditionType](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.accessconditiontype?view=azure-dotnet) |

## <a name="indexing-examples"></a>索引示例
*IndexManagement* 项目示例的 [RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/ea8c977b9c2f37ddc2894911ec239907ab60e40a/samples/code-samples/IndexManagement/Program.cs#L89-L117) 方法演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的索引，请参阅[索引策略](index-policy.md)、[索引类型](index-types.md)和[索引路径](index-paths.md)。 

| 任务 | API 参考 |
| --- | --- |
| [从索引中排除文档](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L125-L163) |[IndexingDirective.Exclude](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexingdirective?view=azure-dotnet) |
| [使用手动（而非自动）索引](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L171-L209) |[IndexingPolicy.Automatic](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexingpolicy.automatic?view=azure-dotnet) |
| [从索引中排除指定的文档路径](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L248-L297) |[IndexingPolicy.ExcludedPaths](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths?view=azure-dotnet) |
| [对哈希索引路径强制执行范围扫描操作](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery?view=azure-dotnet) |
| [对字符串使用范围索引](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L342-L405) |[IndexingPolicy.IncludedPaths](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths?view=azure-dotnet)<br />[RangeIndex](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.rangeindex?view=azure-dotnet) |
| [执行索引转换](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L407-L464) |[ReplaceDocumentCollectionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync?view=azure-dotnet) |

## <a name="geospatial-examples"></a>地理空间示例
地理空间示例文件 [azure-documentdb-dotnet/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)演示如何执行以下任务。  若要在运行以下示例之前了解 GeoJSON 和地理空间数据，请参阅[使用地理空间和 GeoJSON 位置数据](geospatial.md)。 

| 任务 | API 参考 |
| --- | --- |
| [对新集合启用地理空间索引](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L45-L63) |[IndexingPolicy](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexingpolicy?view=azure-dotnet) <br /> [IndexKind.Spatial](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.indexkind?view=azure-dotnet) <br />[DataType.Point](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.datatype?view=azure-dotnet) |
| [使用 GeoJSON 点插入文档](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L116-L126) |[DocumentClient.CreateDocumentAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync?view=azure-dotnet) <br /> [DataType.Point](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.datatype?view=azure-dotnet) |
| [查找指定距离内的点](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L152-L194) |[ST_DISTANCE](how-to-sql-query.md#spatial-functions) <br /> [GeometryOperationExtensions.Distance](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance?view=azure-dotnet) |
| [查找多边形内的点](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L196-L221) |[ST_WITHIN](how-to-sql-query.md#spatial-functions) <br /> [GeometryOperationExtensions.Within](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance?view=azure-dotnet) <br />[多边形](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.spatial.polygon?view=azure-dotnet) |
| [对现有集合启用地理空间索引](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L312-L336) |[DocumentClient.ReplaceDocumentCollectionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync?view=azure-dotnet)<br />[DocumentCollection.IndexingPolicy](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy?view=azure-dotnet) |
| [验证点和多边形数据](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L223-L265) |[ST_ISVALID](how-to-sql-query.md#spatial-functions)<br />[ST_ISVALIDDETAILED](how-to-sql-query.md#spatial-functions)<br />[GeometryOperationExtensions.IsValid](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid?view=azure-dotnet)<br />[GeometryOperationExtensions.IsValidDetailed](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed?view=azure-dotnet) |

## <a name="query-examples"></a>查询示例
查询文档文件 [azure-documentdb-dotnet/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) 演示如何通过 SQL 查询语法以及使用查询和 Lambda 的 LINQ 提供程序执行以下各项任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅 [Azure Cosmos DB 的 SQL 查询示例](how-to-sql-query.md)。 

| 任务 | API 参考 |
| --- | --- |
| [查询所有文档](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L122-L138) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用 == 查询等式](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L251-L268) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用 != 和 NOT 查询不等式](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L270-L276) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用 >、<、>=、<= 等范围运算符进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L305-L325) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用范围运算符对字符串进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L337-L346) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用 ORDER BY 进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L370-L392) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用聚合函数进行查询](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用子文档](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L394-L419) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用文档内联接进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L421-L435) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用字符串、数学和数组运算符进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L527-L552) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [通过使用 SqlQuerySpec 的参数化 SQL 进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L140-L174) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)<br />[SqlQuerySpec](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [使用显式分页进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L554-L576) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [并行查询已分区集合](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L664-L734) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |
| [使用 ORDER BY 针对已分区集合进行查询](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L737-L810) |[DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) |

## <a name="change-feed-examples"></a>更改源示例 
更改源示例 [azure-documentdb-dotnet/samples/code-samples/ChangeFeed/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs) 演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的更改源，请参阅[读取 Azure Cosmos DB 更改源](read-change-feed.md)和[更改源处理器](change-feed-processor.md)。 

| 任务 | API 参考 |
| --- | --- |
| [读取更改源](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) | 
| [读取分区键范围](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet) | 

更改源处理器示例 [ChangeFeedMigrationTool](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedMigrationTool) 演示如何使用更改源处理器库将数据复制到另一个 Cosmos DB 集合。   

## <a name="server-side-programming-examples"></a>服务器端编程示例
服务器端编程文件 [azure-documentdb-dotnet/samples/code-samples/ServerSideScripts/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs)演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的服务器端编程，请参阅[存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建存储过程](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L112) |[DocumentClient.CreateStoredProcedureAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync?view=azure-dotnet) |
| [执行存储过程](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L127) |[DocumentClient.ExecuteStoredProcedureAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync?view=azure-dotnet) |
| [读取存储过程的文档源](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L194) |[DocumentClient.ReadDocumentFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet) |
| [使用 ORDER BY 创建存储过程](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L219) |[DocumentClient.CreateStoredProcedureAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync?view=azure-dotnet) |
| [创建预触发器](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L264) |[DocumentClient.CreateTriggerAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync?view=azure-dotnet) |
| [创建后触发器](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L329) |[DocumentClient.CreateTriggerAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync?view=azure-dotnet) |
| [创建用户定义函数 (UDF)](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L389) |[DocumentClient.CreateUserDefinedFunctionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync?view=azure-dotnet) |

## <a name="user-management-examples"></a>用户管理示例
用户管理文件 [azure-documentdb-dotnet/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/UserManagement/Program.cs) 演示如何执行以下任务：

| 任务 | API 参考 |
| --- | --- |
| [创建用户](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L81) |[DocumentClient.CreateUserAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync?view=azure-dotnet) |
| [对集合或文档设置权限](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L85) |[DocumentClient.CreatePermissionAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync?view=azure-dotnet) |
| [获取用户权限列表](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L218) |[DocumentClient.ReadUserAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync?view=azure-dotnet)<br />[DocumentClient.ReadPermissionFeedAsync](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync?view=azure-dotnet) |

<!-- Update_Description: update meta properties, update link -->