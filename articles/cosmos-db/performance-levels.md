---
title: 已停用的 Azure Cosmos DB 性能级别
description: 了解 Azure Cosmos DB 中以前提供的 S1、S2 和 S3 性能级别。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 06/04/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: 69e3f73c0e9081c35680654c6ad21a819e98b7d9
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627682"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>停用 S1、S2 和 S3 性能级别

> [!IMPORTANT] 
> 本文所述的 S1、S2 和 S3 性能级别即将停用，在新的 Azure Cosmos DB 帐户中将不再提供。
>

本文概述 S1、S2 和 S3 性能级别，并介绍如何将使用这些性能级别的集合迁移到单分区集合。 阅读本文后，可以回答以下问题：

- [为何要停用 S1、S2 和 S3 性能级别？](#why-retired)
- [单区集合和分区集合与 S1、S2、S3 性能级别有哪些区别？](#compare)
- [我需要做些什么才能确保不间断地访问我的数据？](#uninterrupted-access)
- [迁移后，集合会发生怎样的变化？](#collection-change)
  <!-- Not Available - [How will my billing change after I'm migrated to single partition collections?](#billing-change)-->
- [如果需要 10 GB 以上的存储，应该怎么做？](#more-storage-needed)
- [在计划的迁移期限之前，是否可以在 S1、S2 和 S3 性能级别之间切换？](#change-before)
- [如何自行从 S1、S2、S3 性能级别迁移到单区集合？](#migrate-diy)
- [EA 客户会受到怎样的影响？](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>为何要停用 S1、S2 和 S3 性能级别？

S1、S2 和 S3 性能级别无法提供标准 Azure Cosmos DB 产品提供的灵活性。 使用 S1、S2、S3 性能级别时，吞吐量和存储容量都是预设的，没有任何弹性。 现在，Azure Cosmos DB 允许用户自定义吞吐量和存储，大幅提高随需求变化而缩放的灵活性。

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>单区集合和分区集合与 S1、S2、S3 性能级别有哪些区别？

下表比较了单区集合、分区集合以及 S1、S2、S3 性能级别提供的吞吐量和存储选项。 下面是“中国东部 2”区域的一个示例：

|   |分区集合|单区集合|S1|S2|S3|
|---|---|---|---|---|---|
|最大吞吐量|无限制|10K RU/s|250 RU/秒|1 K RU/秒|2.5 K RU/秒|
|最小吞吐量|2.5 K RU/秒|400 RU/s|250 RU/s|1 K RU/秒|2.5 K RU/秒|
|最大存储|无限制|10 GB|10 GB|10 GB|10 GB|

<!-- Not Avaialbel |Price|Throughput: $6 / 100 RU/s<br><br>Storage: $0.25/GB|Throughput: $6 / 100 RU/s<br><br>Storage: $0.25/GB|$25 USD|$50 USD|$100 USD|-->

是 EA 客户吗？ 如果是，请参阅 [如果我是 EA 客户，我会受到怎样的影响？](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>我需要做些什么才能确保不间断地访问我的数据？

如果有 S1、S2 或 S3 集合，则应[使用 .NET SDK](#migrate-diy) 以编程方式将该集合迁移到单分区集合。 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>迁移后，集合会发生怎样的变化？

如果有 S1 集合，可以将该集合迁移到吞吐量为 400 RU/秒的单分区集合。 400 RU/s 是单区集合提供的最低吞吐量。 但是，单分区集合中 400 RU/秒吞吐量的费用大致与 S1 集合中 250 RU/秒吞吐量的费用相同 - 因此，不需要为提供的额外 150 RU/秒付费。

如果有 S2 集合，可以将该集合迁移到吞吐量为 1000 RU/秒的单分区集合。 吞吐量级别没有可见变化。

如果有 S3 集合，可以将该集合迁移到吞吐量为 2500 RU/秒的单分区集合。 吞吐量级别没有可见变化。

对于上面的每种情况，在迁移集合后，便可以根据需要自定义吞吐量级别，或者将它调高和调低，以便向用户提供低延迟的访问。 

<a name="billing-change"></a>

<!--Not Available ## How will my billing change after I'm migrated to the single partition collections?-->


<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>如果需要 10 GB 以上的存储，应该怎么做？

无论使用的是 S1、S2 或 S3 性能级别的集合还是单分区集合，它们的可用存储都是 10 GB。可以使用 Azure Cosmos DB 数据迁移工具将数据迁移到分区集合，它们的存储空间几乎无限。 有关分区集合优势的信息，请参阅 [Azure Cosmos DB 中的分区和缩放](sql-api-partition-data.md)。 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>在计划的迁移期限之前，是否可以在 S1、S2 和 S3 性能级别之间切换？

只有使用 S1、S2 和 S3 性能的现有帐户才能[使用 .NET SDK](#migrate-diy) 以编程方式切换和更改性能级别层。 如果从 S1、S3 或 S3 切换到了单区集合，则无法恢复到 S1、S2 或 S3 性能级别。

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>如何自行从 S1、S2、S3 性能级别迁移到单区集合？

可以[使用 .NET SDK](#migrate-diy) 以编程方式从 S1、S2 和 S3 性能级别迁移到单分区集合。 可以在计划的迁移期限之前自行执行此操作，即可享用单分区集合提供的灵活吞吐量选项。

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>使用 .NET SDK 迁移到单分区集合

本部分只介绍了使用 [SQL .NET API](sql-api-sdk-dotnet.md) 更改集合的性能级别，但对于我们的其他 SDK，过程也是相似的。

以下代码片段可将集合吞吐量更改为每秒 5,000 个请求单位：

```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

请访问 [MSDN](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient) 以查看其他示例并了解更多有关服务方法的信息：

* [**ReadOfferAsync**](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>EA 客户会受到怎样的影响？

EA 客户在当前合同到期之前，价格将受到保护。

## <a name="next-steps"></a>后续步骤
若要了解更多有关 Azure Cosmos DB 的定价和管理数据的信息，请浏览以下资源：

1.  [对 Cosmos DB 中的数据进行分区](sql-api-partition-data.md)。 了解单区容器与分区容器的差异，以及有关实施分区策略以进行无缝缩放的提示。
2.  [Cosmos DB 定价](https://www.azure.cn/pricing/details/cosmos-db/)。 了解预配吞吐量和使用存储的费用。
3.  [请求单位](request-units.md)。 了解不同操作类型（例如读取、写入和查询）的吞吐量消耗。

<!--Update_Description: update meta properties, wording update -->