---
title: 如何在 Azure Cosmos DB 中使用 SQL 进行查询？
description: 了解在 Azure Cosmos DB 中使用 SQL 进行查询
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
origin.date: 05/10/2017
ms.date: 03/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 4cb56414c2c53637e776af81ff55aa683f2685a0
ms.sourcegitcommit: b56dae931f7f590479bf1428b76187917c444bbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56987918"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>教程：使用 SQL API 查询 Azure Cosmos DB

Azure Cosmos DB [SQL API](documentdb-introduction.md) 支持使用 SQL 查询文档。 本文提供一个示例文档和两个示例 SQL 查询和结果。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 SQL 查询数据

## <a name="sample-document"></a>示例文档

本文中的 SQL 查询使用下面的示例文档。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>可在何处运行 SQL 查询？

通过 [REST API 和 SDK](sql-api-sdk-dotnet.md) 或[查询演练](https://www.documentdb.com/sql/demo)（它对现有示例数据集运行查询），可在 Azure 门户中使用数据资源管理器运行查询。

有关 SQL 查询的详细信息，请参阅：
* [SQL 查询和 SQL 语法](how-to-sql-query.md)

## <a name="prerequisites"></a>先决条件

本教程假定已拥有 Azure Cosmos DB 帐户和集合。 没有这些内容？ 完成 [5 分钟快速入门](create-mongodb-nodejs.md)。

## <a name="example-query-1"></a>示例查询 1

若使用上述示例家庭文档，则以下 SQL 查询返回其 ID 字段匹配 `WakefieldFamily` 的文档。 由于它是 `SELECT *` 语句，因此该查询的输出为完整的 JSON 文档：

**查询**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**结果**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>示例查询 2

下一查询返回家族中所有 ID 匹配 `WakefieldFamily` 的子女的名字（按匹配得分排序）。

**查询**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**结果**

[ { "givenName":"Jesse" }, { "givenName":"Lisa" } ]

## <a name="next-steps"></a>后续步骤

在本教程中已完成以下操作：

> [!div class="checklist"]
> * 已了解如何使用 SQL 进行查询  

现可继续学习下一教程，了解如何多区域分发数据。

> [!div class="nextstepaction"]
> [全局分发数据](tutorial-global-distribution-sql-api.md)

<!-- Update_Description: update meta properties -->
