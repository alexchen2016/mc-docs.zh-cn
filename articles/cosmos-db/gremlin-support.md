---
title: Azure Cosmos DB Gremlin 支持
description: 了解 Apache TinkerPop 的 Gremlin 语言。 了解 Azure Cosmos DB 中提供了哪些功能和步骤
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
origin.date: 05/21/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 6b9b34be0c9812bd56b99c6ea8d9c6fffd9d0ddf
ms.sourcegitcommit: 43eb6282d454a14a9eca1dfed11ed34adb963bd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151445"
---
<!--Verify sucessfully-->
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Azure Cosmos DB Gremlin 图形支持
Azure Cosmos DB 支持 [Apache Tinkerpop](https://tinkerpop.apache.org) 的图形遍历语言（称为 [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)）。 可以使用 Gremlin 语言创建图形实体（顶点和边缘）、修改这些实体内部的属性、执行查询和遍历，以及删除实体。 

Azure Cosmos DB 为图形数据库提供企业级的功能。 这些功能包括多区域分布、存储和吞吐量独立缩放、低至个位数的可预测延迟、自动编制索引、SLA、跨越两个或更多 Azure 区域的数据库帐户的读取可用性。 由于 Azure Cosmos DB 支持 TinkerPop/Gremlin，因此可以轻松迁移使用其他兼容图形数据库编写的应用程序。 此外，由于具有 Gremlin 支持，Azure Cosmos DB 可与支持 TinkerPop 的分析框架（例如 [Apache Spark GraphX](https://spark.apache.org/graphx/)）无缝集成。 

本文提供 Gremlin 的快速演练，并列举 Gremlin API 支持的 Gremlin 功能。

## <a name="gremlin-by-example"></a>举例介绍 Gremlin
我们使用一个示例图形来了解如何在 Gremlin 中表示查询。 下图显示了一个商业应用程序，该应用程序管理以图形形式呈现的有关用户、兴趣和设备的数据。  

![显示人员、设备和兴趣的示例数据库](./media/gremlin-support/sample-graph.png) 

此图形使用以下顶点类型（在 Gremlin 中称为“标签”）：

- 人员：图形中包含三名人员：Robin、Thomas 和 Ben
- 兴趣：在此示例中，人员的兴趣为足球比赛
- 设备：人员使用的设备
- 操作系统：设备在其上运行的操作系统

我们通过以下边缘类型/标签表示这些实体之间的关系：

- 认识：例如，“Thomas 认识 Robin”
- 感兴趣的内容：在图形中表示人员的兴趣，例如，“Ben 对足球感兴趣”
- RunsOS：运行 Windows OS 的笔记本电脑
- 使用：表示人员使用哪种设备。 例如，Robin uses a Motorola phone with serial number 77

让我们使用 [Gremlin 控制台](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console)对此图形运行一些操作。 也可以在所选的平台（Java、Node.js、Python 或 .NET）中使用 Gremlin 驱动程序执行这些操作。  在了解 Azure Cosmos DB 支持的功能之前，先通过几个示例来熟悉语法。

首先，让我们了解 CRUD。 以下 Gremlin 语句在图形中插入“Thomas”顶点：

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

接下来，以下 Gremlin 语句在 Thomas 与 Robin 之间插入“knows”边缘。

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

以下查询按人员名字的降序返回“person”顶点：
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

如果需要回答类似于“Thomas 的朋友使用哪些操作系统？”的问题，图形可以提供很大的方便。 可以运行此 Gremlin 遍历从图形中获取该信息：

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
现在，看看 Azure Cosmos DB 为 Gremlin 开发人员提供哪些信息。

## <a name="gremlin-features"></a>Gremlin 的功能
TinkerPop 是涵盖多种图形技术的标准。 因此，它使用标准的术语来描述图形提供程序提供的功能。 Azure Cosmos DB 提供一个可跨多个服务器或群集分区的持久性、高并发性、可写的图形数据库。 

下表列出了 Azure Cosmos DB 实现的 TinkerPop 功能： 

| 类别 | Azure Cosmos DB 实现 |  说明 | 
| --- | --- | --- |
| 图形功能 | 提供持久性和并发访问。 旨在支持事务 | 可通过 Spark 连接器实现计算机方法。 |
| 变量功能 | 支持布尔值、整数、字节、双精度值、浮点值、长整数和字符串 | 支持基元类型，通过数据模型与复杂类型兼容 |
| 顶点功能 | 支持 RemoveVertices、MetaProperties、AddVertices、MultiProperties、StringIds、UserSuppliedIds、AddProperty、RemoveProperty  | 支持创建、修改和删除顶点 |
| 顶点属性功能 | StringIds、UserSuppliedIds、AddProperty、RemoveProperty、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除顶点属性 |
| 边缘功能 | AddEdges、RemoveEdges、StringIds、UserSuppliedIds、AddProperty、RemoveProperty | 支持创建、修改和删除边缘 |
| 边缘属性功能 | Properties、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除边缘属性 |

## <a name="gremlin-wire-format-graphson"></a>Gremlin 网络格式：GraphSON

从 Gremlin 操作返回结果时，Azure Cosmos DB 使用 [GraphSON 格式](https://tinkerpop.apache.org/docs/3.3.2/reference/#graphson-reader-writer)。 GraphSON 是 Gremlin 标准格式，它使用 JSON 来表示顶点、边缘和属性（单值和多值属性）。 

例如，以下代码片段显示了从 Azure Cosmos DB *返回到客户端*的某个顶点的 GraphSON 表示形式。 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

下面介绍 GraphSON 使用的顶点属性：

| 属性 | 说明 | 
| --- | --- |
| `id` | 顶点的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并）。 如果未提供任何值，则系统会自动提供一个包含 GUID 的值 | 
| `label` | 顶点的标签。 此属性用于描述实体类型。 |
| `type` | 用于将顶点与非图形文档相区分 |
| `properties` | 与顶点关联的用户定义属性包。 每个属性可以有多个值。 |
| `_partition` | 顶点的分区键。 用于[图形分区](graph-partitioning.md)。 |
| `outE` | 此属性包含顶点中外部边缘的列表。 存储顶点的相邻信息，以便快速执行遍历。 边缘根据其标签分组。 |

边缘包含以下信息，以方便导航到图形的其他部件。

| 属性 | 说明 |
| --- | --- |
| `id` | 边缘的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并） |
| `label` | 边缘的标签。 此属性是可选的，用于描述关系类型。 |
| `inV` | 此属性包含边缘的一系列顶点。 存储顶点的相邻信息可以快速执行遍历。 顶点根据其标签分组。 |
| `properties` | 与边缘关联的用户定义属性包。 每个属性可以有多个值。 |

每个属性可在一个数组中存储多个值。 

| 属性 | 说明 |
| --- | --- |
| `value` | 属性的值 |

## <a name="gremlin-steps"></a>Gremlin 的步骤
现在，让我们了解 Azure Cosmos DB 支持的 Gremlin 步骤。 有关 Gremlin 的完整参考信息，请参阅 [TinkerPop 参考](https://tinkerpop.apache.org/docs/3.3.2/reference)。

| 步骤 | Description | TinkerPop 3.2 文档 |
| --- | --- | --- |
| `addE` | 在两个顶点之间添加边缘 | [addE 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | 将顶点添加到图形 | [addV 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | 确保所有遍历都返回值 | [and 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | 用于向步骤的输出分配变量的步骤调制器 | [as 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | 与 `group` 和 `order` 配合使用的步骤调制器 | [by 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 返回第一个返回结果的遍历 | [coalesce 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 返回常量值。 与 `coalesce` 配合使用| [constant 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | 从遍历返回计数 | [count 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 返回已删除重复内容的值 | [dedup 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 丢弃值（顶点/边缘） | [drop 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 创建执行的 Gremlin 步骤生成的所有操作的说明 | [executionProfile 步骤](graph-execution-profile.md) |
| `fold` | 充当用于计算结果聚合值的屏障| [fold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 根据指定的标签将值分组| [group 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | 用于筛选属性、顶点和边缘。 支持 `hasLabel`、`hasId`、`hasNot` 和 `has` 变体。 | [has 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 将值注入流中| [inject 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | 用于通过布尔表达式执行筛选器 | [is 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | 用于限制遍历中的项数| [limit 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | 本地包装遍历的某个部分，类似于子查询 | [local 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | 用于生成筛选器的求反结果 | [not 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 如果生成了某个结果，则返回指定遍历的结果，否则返回调用元素 | [optional 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 确保至少有一个遍历会返回值 | [or 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 按指定的排序顺序返回结果 | [order 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | 返回遍历的完整路径 | [path 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | 将属性投影为映射 | [project 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 返回指定标签的属性 | [properties 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 根据指定的值范围进行筛选| [range 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | 将步骤重复指定的次数。 用于循环 | [repeat 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | 用于对遍历返回的结果采样 | [sample 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | 用于投影遍历返回的结果 |  [select 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | 用于遍历返回的非阻塞聚合 | [store 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的开头进行匹配 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的结尾进行匹配 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的内容进行匹配 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串开头的属性 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串结尾的属性 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不包含给定字符串的属性 | [TextP 谓词](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | 将顶点中的路径聚合到树中 | [tree 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 将迭代器作为步骤展开| [unfold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 合并多个遍历返回的结果| [union 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 包括顶点与边缘之间的遍历所需的步骤：`V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV` 和 `otherV` | [vertex 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | 用于筛选遍历返回的结果。 支持 `eq`、`neq`、`lt`、`lte`、`gt`、`gte` 和 `between` 运算符  | [where 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB 提供的写入优化引擎默认支持自动对顶点和边缘中的所有属性编制索引。 因此，使用筛选器、范围查询、排序或聚合对任何属性执行的查询将从索引处理，并可有效完成。 有关 Azure Cosmos DB 中索引编制的工作原理的详细信息，请参阅有关[架构不可知的索引编制](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)的文章。

## <a name="next-steps"></a>后续步骤
* 开始[使用我们的 SDK](create-graph-dotnet.md) 构建图形应用程序 
* 详细了解 Azure Cosmos DB 中的[图形支持](graph-introduction.md)

<!--Update_Description: wording update -->
