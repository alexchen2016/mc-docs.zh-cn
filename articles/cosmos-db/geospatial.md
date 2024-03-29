---
title: 在 Azure Cosmos DB SQL API 帐户中使用地理空间数据
description: 了解如何使用 Azure Cosmos DB 和 SQL API 创建、索引和查询空间对象。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 05/23/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 54b4a40ec9dff319f0cad82a2f45915b48ddc137
ms.sourcegitcommit: 43eb6282d454a14a9eca1dfed11ed34adb963bd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151446"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>在 Azure Cosmos DB SQL API 帐户中使用地理空间和 GeoJSON 位置数据

本文介绍 Azure Cosmos DB 中的地理空间功能。 目前，仅 Cosmos DB SQL API 帐户支持存储和访问地理空间数据。 阅读本文后，能够回答以下问题：

* 如何在 Azure Cosmos DB 中存储空间数据？
* 如何使用 SQL 和 LINQ 查询 Azure Cosmos DB 中的地理空间数据？
* 如何在Azure Cosmos DB 中启用或禁用空间索引？

本文介绍如何通过 SQL API 使用空间数据。 请参阅此 [GitHub 项目](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)中的代码示例。

## <a name="introduction-to-spatial-data"></a>空间数据简介
空间数据用于描述空间中对象的位置和形状。 在大部分应用程序中，这些会对应于地球上的对象，也就是地理空间数据。 空间数据可以用来表示人、名胜古迹、城市边界或湖泊所处的位置。 常见用例通常涉及邻近查询，例如“寻找我目前位置附近的所有咖啡厅”。 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB 支持对使用 [GeoJSON 规范](https://tools.ietf.org/html/rfc7946)表示的地理空间点数据进行索引编制和查询。 GeoJSON 数据结构永远是有效的 JSON 对象，因此可以通过 Azure Cosmos DB 来存储和查询，无需任何专用的工具或库。 Azure Cosmos DB SDK 提供帮助程序类和方法，让空间数据更易使用。 

### <a name="points-linestrings-and-polygons"></a>点、线串和多边形
**点** 代表空间中的单一位置。 在地理空间数据中，某个点所代表的确切位置可能是杂货店、电话亭、汽车或城市的街道地址。  点使用其坐标对或经纬度，以 GeoJSON 格式（和 Azure Cosmos DB）表示。 以下是点的 JSON 示例。

Azure Cosmos DB 中的点 

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON 规范先指定经度，再指定纬度。 与其他地图应用程序一样，经度和纬度为角度，并以度为单位表示。 经度值从本初子午线测量，并介于 -180 度和 180.0 度之间；纬度值从赤道测量，并介于 -90.0 度和 90.0 度之间。 
> 
> Azure Cosmos DB 会将坐标解释为按照 WGS-84 参考系统表示。 有关坐标参考系统的更多详细信息，请参阅下文。
> 
> 

如下面包含位置数据的用户配置文件示例所示，此数据可以嵌入 Azure Cosmos DB 文档中：

存储在 Azure Cosmos DB 中包含位置的用户配置文件 

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

除了点之外，GeoJSON 也支持 LineString 和多边形。 **LineString** 表示空间中一连串的点（两个或更多个）以及连接这些点的线段。 在地理空间数据中，LineString 通常用来表示高速公路或河流。 **多边形** 是形成闭合的 LineString 的相连接的点的边界。 多边形通常用来表示自然构成物（例如湖泊），或表示政治管辖权（例如省/市/自治区）。 以下是 Azure Cosmos DB 中多边形的示例。 

GeoJSON 中的多边形 

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 规范需要此数据才能形成有效的多边形；若要创建一个闭合的形状，最后一个坐标对应该与第一个坐标对相同。
> 
> 多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。
> 
> 

除了点、LineString 和多边形之外，GeoJSON 也会针对如何对多个地理空间位置分组以及如何将任意属性与地理位置关联成为**特征**指定表示形式。 由于这些对象都是有效的 JSON，因此均可在 Azure Cosmos DB 中存储及处理。 不过，Azure Cosmos DB 仅支持自动编制点的索引。

### <a name="coordinate-reference-systems"></a>坐标参考系统
由于地球的形状不规则，地理空间数据的坐标可以用许多坐标参考系统 (CRS) 来表示，而这些系统各有自己的参考框架和测量单位。 例如，“英国国家网格”对英国而言是非常精确的参考系统，但对其他地区则不是。 

现今最常使用的 CRS 是世界测地系统 [WGS-84](http://earth-info.nga.mil/GandG/wgs84/)。 GPS 设备和许多地图服务（包括谷歌地图和必应地图 API）均使用 WGS-84。 Azure Cosmos DB 仅支持对使用 WGS-84 CRS 的地理空间数据进行索引编制和查询。 

## <a name="creating-documents-with-spatial-data"></a>创建包含空间数据的文档
创建包含 GeoJSON 值的文档时，值会根据容器的索引策略，自动以空间索引进行索引编制。 如果以动态类型化语言（如 Python 或 Node.js）使用 Azure Cosmos DB SDK，则必须创建有效的 GeoJSON。

**在 Node.js 中创建包含地理空间数据的文档**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

如果使用 SQL API，则可以在 `Microsoft.Azure.Documents.Spatial` 命名空间中使用 `Point` 和 `Polygon` 类，将位置信息嵌入应用程序对象中。 这些类有助于简化将空间数据序列化和反序列化为 GeoJSON 的过程。

**在 .NET 中创建包含地理空间数据的文档**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

如果没有经纬度信息，但有物理地址或位置名称，如城市或国家/地区，则可以使用必应地图 REST 服务等地理编码服务来查找实际的坐标。 在[此处](https://msdn.microsoft.com/library/ff701713.aspx)详细了解必应地图地理编码。

## <a name="querying-spatial-types"></a>查询空间类型
探讨过如何插入地理空间数据之后，现在来看看如何通过 SQL 和 LINQ 使用 Azure Cosmos DB 查询此数据。

### <a name="spatial-sql-built-in-functions"></a>空间 SQL 内置函数
Azure Cosmos DB 支持以下用于查询地理空间的开放地理空间信息联盟 (OGC) 内置函数。 有关 SQL 语言中的整套内置函数的详细信息，请参阅[查询 Azure Cosmos DB](how-to-sql-query.md)。

|**使用情况**|**说明**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | 返回两个 GeoJSON 点、多边形或 LineString 表达式之间的距离。|
|ST_WITHIN (spatial_expr, spatial_expr) | 返回一个布尔表达式，该表达式指示第一个 GeoJSON 对象（点、多边形或 LineString）是否在第二个 GeoJSON 对象（点、多边形或 LineString）内。|
|ST_INTERSECTS (spatial_expr, spatial_expr)| 返回一个布尔表达式，该表达式指示两个指定的 GeoJSON 对象（点、多边形或 LineString）是否相交。|
|ST_ISVALID| 返回一个布尔值，该值指示指定的 GeoJSON 点、多边形或 LineString 表达式是否有效。|
| ST_ISVALIDDETAILED| 如果指定的 GeoJSON 点、多边形或 LineString 表达式有效，则返回包含布尔值的 JSON 值；如果无效，则额外加上作为字符串值的原因。|

空间函数可用于对空间数据执行邻近查询。 例如，以下查询使用 ST_DISTANCE 内置函数返回所有家族文档，且这些文档在指定位置的 30 公里内。 

**查询**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**结果**

    [{
      "id": "WakefieldFamily"
    }]

如果索引策略中包含空间索引，则通过索引有效地进行“距离查询”。 有关空间索引的详细信息，请参阅以下部分。 如果没有指定路径的空间索引，仍然可以通过指定 `x-ms-documentdb-query-enable-scan` 请求标头（其值设置为“true”）执行空间查询。 在 .NET 中，可以通过将可选的 **FeedOptions** 参数传递到 [EnableScanInQuery](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) 设置为 true 的查询来完成此操作。 

ST_WITHIN 可用于检查点是否在多边形内。 多边形通常用于表示边界，例如邮政编码、省/自治区边界或自然构成物。 再次说明，如果在索引策略中包含空间索引，则将通过索引有效地进行“within”查询。 

ST_WITHIN 中的多边形参数只能包含单个环形，也就是说，多边形本身不能包含洞。 

**查询**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**结果**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> 与 Azure Cosmos DB 查询中不匹配类型的工作方式类似，如果任一参数中指定的位置值格式不正确或无效，则会评估为**未定义**，并且会在查询结果中跳过已评估的文档。 如果查询没有返回任何结果，请运行 ST_ISVALIDDETAILED 进行调试，以了解空间类型无效的原因。     
> 
> 

Azure Cosmos DB 还支持执行反向查询，即可在 Azure Cosmos DB 中索引多边形或线，并查询包含指定点的区域。 这种模式通常在物流中用于识别如卡车进入或离开指定区域的时间等情况。 

**查询**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)

**结果**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID 和 ST_ISVALIDDETAILED 可用来检查空间对象是否有效。 例如，下列查询检查纬度值 (-132.8) 超出范围的点的有效性。 ST_ISVALID 仅返回一个布尔值，ST_ISVALIDDETAILED 则返回布尔值和字符串，字符串中包含被视为无效的原因。

**查询**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**结果**

    [{
      "$1": false
    }]

这些函数也可以用来验证多边形。 例如，在这里我们使用 ST_ISVALIDDETAILED 来验证未闭合的多边形。 

**查询**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**结果**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>.NET SDK 中的 LINQ 查询
SQL .NET SDK 还提供存根方法 `Distance()` 和 `Within()`，供用户在 LINQ 表达式中使用。 SQL LINQ 提供程序会将这些方法调用转换为等效的 SQL 内置函数调用（分别为 ST_DISTANCE 和 ST_WITHIN）。 

以下是 LINQ 查询的示例，该查询使用 LINQ 在 Azure Cosmos DB 集合中查找所有文档，这些文档的“位置”值在指定点的 30 公里半径内。

**LINQ 距离查询**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

同样地，以下查询查找所有文档，这些文档的“位置”均在指定的方框/多边形内。 

**LINQ Within 查询**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }

探讨过如何使用 LINQ 和 SQL 查询文档之后，现在来看看如何针对空间索引配置 Azure Cosmos DB。

## <a name="indexing"></a>索引
如[使用 Azure Cosmos DB 进行架构不可知的索引](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)一文中所述，Azure Cosmos DB 数据库引擎具有真正不可知的架构，并提供一流的 JSON 支持。 Azure Cosmos DB 的写入优化数据库引擎可以通过本机方式了解用 GeoJSON 标准表示的空间数据（点、多边形和线）。

简单来说，测地坐标的几何图形会投影在 2D 平面上，并使用**四叉树**以渐进方式划分成单元格。 这些单元格会根据 **Hilbert 空间填充曲线**内的单元格位置映射到 1D，并保留点的位置。 此外，当位置数据进行索引编制后，会经历称为 **分割**的过程，也就是说，在某个位置上相交的所有单元格都会被识别为键并存储在 Azure Cosmos DB 索引中。 在查询时，点和多边形等参数也会经过分割，以提取相关的格子 ID 范围，并用于从索引检索数据。

如果指定的索引策略包含 /*（所有路径）的空间索引，则表示在集合中找到的所有点均已编制索引，能进行有效的空间查询（ST_WITHIN 和 ST_DISTANCE）。 空间索引没有精度值，并且始终使用默认的精度值。

> [!NOTE]
> Azure Cosmos DB 支持点、多边形和 LineString 的自动索引
> 
> 

以下 JSON 代码段演示已启用空间索引的索引策略，也就是为文档中找到的所有 GeoJSON 点编制索引，以用于空间查询。 如果要使用 Azure 门户修改索引策略，可以为索引策略指定以下 JSON，以便对集合启用空间索引。

**针对点和多边形启用了空间索引的集合索引策略 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

以下是 .NET 中的代码段，演示如何创建针对所有包含点的路径启用空间索引的集合。 

**创建具有空间索引的集合**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

下面说明了如何修改现有的集合，以便对文档内存储的所有点使用空间索引。

**修改具有空间索引的现有集合**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> 如果文档中的 GeoJSON 位置值格式不正确或无效，则不会为其编制索引以用于空间查询。 可以使用 ST_ISVALID 和 ST_ISVALIDDETAILED 验证位置值。
> 
> 如果集合定义包含分区键，则不会报告索引转换进度。 
> 
> 

## <a name="next-steps"></a>后续步骤
已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 使用 [GitHub 上的地理空间 .NET 代码示例](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)开始编写代码
* 在 [Azure Cosmos DB 查询板块](https://www.documentdb.com/sql/demo#geospatial)中实际操作地理空间查询
* 详细了解 [Azure Cosmos DB 查询](how-to-sql-query.md)
* 详细了解 [Azure Cosmos DB 索引策略](index-policy.md)

<!-- Update_Description: update meta properties, wording update -->