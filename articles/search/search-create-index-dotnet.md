---
title: 快速入门：在 C# 控制台应用程序中创建索引 - Azure 搜索
description: 了解如何通过 Azure 搜索 .NET SDK 使用 C# 创建全文可搜索索引。
author: heidisteen
manager: cgronlun
ms.author: v-biyu
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
origin.date: 05/16/2019
ms.date: 06/03/2019
ms.openlocfilehash: 0632f2e264bc49b47fd323757145c469e431c28c
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004633"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>快速入门：1 - 使用 C# 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-fiddler.md)
>*

本文介绍了使用 C# 和 [.NET SDK](https://aka.ms/search-sdk) 创建 [Azure 搜索索引](search-what-is-an-index.md)的过程。 这是第一课，分为创建、加载和查询索引三部分练习。 执行以下任务，完成索引创建：

> [!div class="checklist"]
> * 创建 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 对象以连接到搜索服务。
> * 创建 [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 对象以将其作为参数传递给 `Indexes.Create`。
> * 调用 `SearchServiceClient` 上的 `Indexes.Create` 方法，将 `Index` 发送给服务。

## <a name="prerequisites"></a>先决条件

本快速入门使用以下服务、工具和数据。 

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.cn/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

+ [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)（版本不限）。 示例代码和说明已在免费社区版上进行了测试。

+ [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 提供了示例解决方案，一个用 C# 编写的 .NET Core 控制台应用程序，位于 Azure 示例 GitHub 存储库中。 下载并提取该解决方案。 默认情况下，解决方案是只读的。 右键单击解决方案并清除只读属性，以便可以修改文件。 数据包含在解决方案中。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

对服务的调用要求每个请求都有一个 URL 终结点和一个访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.cn/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.chinacloudapi.cn`。

2. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-fiddler/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="1---configure-and-build"></a>1 - 配置和生成

1. 在 Visual Studio 中打开 **DotNetHowTo.sln** 文件。

1. 在 appsettings.json 中，使用以下示例替换默认内容，然后为服务提供服务名称和管理员 API 密钥。 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  对于服务名称，只需要名称本身。 例如，如果你的 URL 为 https://mydemo.search.windows.net，请将 `mydemo` 添加到 JSON 文件。

1. 按 F5 生成解决方案并运行控制台应用。 本练习中的其余步骤以及后续步骤将介绍此代码的工作原理。 

也可参阅[如何通过 .NET 应用程序使用 Azure 搜索](search-howto-dotnet-sdk.md)，了解 SDK 行为覆盖范围的详细信息。 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - 创建客户端

要开始使用 Azure 搜索 .NET SDK，请创建 `SearchServiceClient` 类的实例。 此类具有几个构造函数。 需要将搜索服务名称和 `SearchCredentials` 对象用作参数。 `SearchCredentials` 包装 API 密钥。

可以在 Program.cs 文件找到以下代码。 它使用应用程序的配置文件 (appsettings.json) 中存储的搜索服务名称和 API 密钥的值创建新的 `SearchServiceClient`。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` 具有 `Indexes` 属性。 此属性提供创建、列出、更新或删除 Azure 搜索索引所需的所有方法。

> [!NOTE]
> `SearchServiceClient` 类管理与搜索服务的连接。 为了避免打开太多连接，应尝试在应用程序中共享 `SearchServiceClient` 的单个实例（如果可能）。 它的方法在启用此类共享时是线程安全的。
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - 构造索引
调用一次 `Indexes.Create` 方法，即可创建索引。 此方法以定义 Azure 搜索索引的 `Index` 对象作为参数。 创建 `Index` 对象，并对其进行初始化，如下所示：

1. 将 `Index` 对象的 `Name` 属性设置为索引的名称。

2. 将 `Index` 对象的 `Fields` 属性设置为 `Field` 对象的数组。 若要创建 `Field` 对象，最简单的方法是调用 `FieldBuilder.BuildForType`，为类型参数传递模型类。 模型类具有映射到索引的字段属性。 因此，可将来自搜索索引的文档绑定到模型类的实例。

> [!NOTE]
> 如果不打算使用模型类，仍可通过直接创建 `Field` 对象来定义索引。 可以向构造函数提供字段的名称以及数据类型（或分析器（用于字符串字段））。 此外可以设置其他属性，如 `IsSearchable`、`IsFilterable`，仅举几例。
>
>

设计索引时，必须牢记搜索用户体验和业务需求。 必须为每个字段分配[属性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)，用于控制哪些搜索功能（筛选、分面、排序等）适用于哪些字段。 对于未显式设置的任何属性，`Field` 类默认禁用相应的搜索功能，除非用户专门启用它。

在此示例中，索引名称为“酒店”，并且字段使用模型类进行定义。 模型类的每个属性都具有一些特性，这些特性决定了相应索引字段的与搜索相关的行为。 模型类定义如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

根据每个字段在应用程序中的预期使用方式，谨慎选择每个属性的特性。 例如，搜索酒店的人员很可能对匹配 `description` 字段的关键字感兴趣，因此通过将 `IsSearchable` 特性添加到 `Description` 属性来为该字段启用全文搜索。

请注意，必须通过添加 `Key` 特性将索引中类型为 `string` 的一个字段指定为“key”字段（请参见上例中的 `HotelId`）。

上述索引定义对 `description_fr` 字段使用了语言分析器，因为它用于存储法语文本。 有关详细信息，请参阅[向 Azure 搜索索引添加语言分析器](index-add-language-analyzers.md)。

> [!NOTE]
> 默认情况下，模型类中每个属性的名称都与索引中的字段名称相对应。 要将所有属性名映射到 camel 形式的字段名称，请使用 `SerializePropertyNamesAsCamelCase` 特性标记类。 若要映射到不同的名称，可以使用 `JsonProperty` 属性，如上面的 `DescriptionFr` 属性一样。 `JsonProperty` 特性优先于 `SerializePropertyNamesAsCamelCase` 特性。
> 
> 

定义了模型类后，现在便可轻松创建索引定义：

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - 调用 Indexes.Create
获得已初始化的 `Index` 对象之后，即可通过对 `SearchServiceClient` 对象调用 `Indexes.Create` 来创建索引：

```csharp
serviceClient.Indexes.Create(definition);
```

对于成功的请求，该方法将正常返回。 如果请求有问题（例如，参数无效），该方法将引发 `CloudException`。

创建完索引并想要删除它时，只需对 `SearchServiceClient` 调用 `Indexes.Delete` 方法。 例如：

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> 为简单起见，本文中的示例代码使用 Azure 搜索 .NET SDK 的同步方法。 建议用户在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，在上面的示例中可以使用 `CreateAsync` 和 `DeleteAsync`，而不是 `Create` 和 `Delete`。
> 
> 

## <a name="next-steps"></a>后续步骤
在本快速入门中，根据定义字段数据类型和行为的架构创建了空的 Azure 搜索索引。 该索引是一个“基本功能”索引，由一个名称和一个属性字段集合组成。 更实际的索引将包括其他元素，例如[计分概要文件](index-add-scoring-profiles.md)、[建议器](index-add-suggesters.md)（用于提前键入支持）、[同义词](search-synonyms.md)，可能还有[自定义分析器](index-add-custom-analyzers.md)。 我们建议你在了解基本工作流之后重新访问这些功能。

本系列的下一个快速入门将介绍如何使用可搜索的内容加载索引。

> [!div class="nextstepaction"]
> [使用 C# 将数据加载到 Azure 搜索索引](search-import-data-dotnet.md)