---
title: Azure Cosmos DB：批量执行程序 Java API、SDK 和资源
description: 了解有关批量执行程序 Java API 的所有信息。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
origin.date: 11/21/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 8192e94e2fb64554e50303c22b327ab9f8e5b60c
ms.sourcegitcommit: b56dae931f7f590479bf1428b76187917c444bbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56987923"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 批量执行程序库：下载信息

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [异步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行程序 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**说明**|批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供 BulkImport 和 BulkUpdate 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。|
|**SDK 下载**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 中的 BulkExecutor 库**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 文档**| [.Net API 参考文档](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**入门**|[批量执行程序库 Java SDK 入门](bulk-executor-java.md)|
|受支持的最小运行时|[Java 开发工具包 (JDK) 7+](https://docs.azure.cn/zh-cn/java/java-supported-jdk-runtime?view=azure-java-stable)|

<!-- Update_Description: update meta properties, update link -->