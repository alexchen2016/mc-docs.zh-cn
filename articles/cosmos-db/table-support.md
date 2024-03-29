---
title: Azure Cosmos DB 中的 Azure 表存储支持
description: 了解 Azure Cosmos DB 表 API 和 Azure 存储表如何协同工作。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
origin.date: 11/15/2017
ms.date: 04/15/2019
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 0936e03408954697a382ef2aac72e3da75562b2a
ms.sourcegitcommit: f85e05861148b480d6c9ea95ce84a17145872442
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615179"
---
<!--Verify sucessfully-->
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 表 API 进行开发

当前，[Azure Cosmos DB 表 API](table-introduction.md) 具有四个可用于进行开发的 SDK： 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. 此库面向 .NET Standard，不仅与公共 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，而且还可以使用表 API 连接到 Azure Cosmos DB 帐户。 或者，可以使用此 SDK 的以前版本（即 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/)，它仅适用于 .NET Framework）。

* [Python SDK](table-sdk-python.md)：新的 Azure Cosmos DB Python SDK 是在 Python 中唯一支持 Azure 表存储的 SDK。 此 SDK 与 Azure 表存储和 Azure Cosmos DB 表 API 连接。

* [Java SDK](table-sdk-java.md)：此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

* [Node.js SDK](table-sdk-nodejs.md)：此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

有关使用表 API 的其他信息可在[常见问题解答：使用表 API 进行开发](faq.md#table)一文中找到。

## <a name="developing-with-azure-table-storage"></a>使用 Azure 表存储进行开发

Azure 表存储的以下 SDK 可用于开发：

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。 该库使你能够使用存储表服务。
- [Python SDK](table-sdk-python.md)。 用于 Python 的 Azure Cosmos DB 表 SDK 也支持存储表服务。
- [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)。 此 Azure 存储 SDK 提供了一个 Java 客户端库来使用 Azure 表存储。
- [Node.js SDK](table-sdk-nodejs.md)。 此 SDK 提供了一个 Node.js 包和一个浏览器兼容的 JavaScript 客户端库来使用存储表服务。
- [AzureRmStorageTable PowerShell 模块](https://www.powershellgallery.com/packages/AzureRmStorageTable)。 此 PowerShell 模块包含 cmdlet 以使用存储表。
- [适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/)。 此库使你能够针对 Azure 存储构建应用程序。
- [适用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)。 此项目提供了一个 Ruby 包，使用该包可轻松访问 Azure 存储表服务。
- [Azure 存储表 PHP 客户端库](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 此项目提供了一个 PHP 客户端库，使用该库可轻松访问 Azure 存储表服务。

<!--Update_Description: new articles on table support -->
<!--ms.date: 03/18/2019-->