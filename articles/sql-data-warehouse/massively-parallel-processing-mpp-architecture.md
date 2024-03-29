---
title: Azure SQL 数据仓库 - MPP 体系结构 | Microsoft Docs
description: 了解 Azure SQL 数据仓库如何将大规模并行处理 (MPP) 与 Azure 存储结合，实现高性能和可伸缩性。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 821a66d65a8b716cae72e28e3acfef9f00060d6c
ms.sourcegitcommit: 4d78c9881b553cd8feecb5555efe0de708545a63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151749"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL 数据仓库 - 大规模并行处理 (MPP) 体系结构
了解 Azure SQL 数据仓库如何将大规模并行处理 (MPP) 与 Azure 存储结合，实现高性能和可伸缩性。 

## <a name="mpp-architecture-components"></a>MPP 体系结构组件
SQL 数据仓库利用向外扩展体系结构在多个节点间分布数据的计算处理。 缩放单位是计算能力（称为[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)）的抽象概念。 SQL 数据仓库将计算与存储分离开来，使用户能够独立于系统中的数据进行规模计算。

![SQL 数据仓库体系结构](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL 数据仓库使用基于节点的体系结构。 应用程序将 T-SQL 命令连接到、发布给控制节点，该节点是数据仓库的单一入口点。 控制节点运行用于优化并行处理查询的 MPP 引擎，然后将操作传递给计算节点以实现并行工作。 计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。 

使用脱偶联的存储和计算，SQL 数据仓库可以：

* 无论存储需求如何，都可独立计算大小。
* 无需移动数据，即可增加或减少计算能力。
* 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
* 在操作期间恢复计算容量。

### <a name="azure-storage"></a>Azure 存储
SQL 数据仓库使用 Azure 存储保护用户数据。  由于数据通过 Azure 存储进行存储和管理，因此 SQL 数据仓库对存储消耗单独收费。 将数据本身分片到“分布区”中来优化系统性能  。 可选择在定义表时用于分布数据的分片模式。 SQL 数据仓库支持以下分片模式：

* 哈希
* 轮循机制
* 复制

### <a name="control-node"></a>控制节点

控制节点是数据仓库的核心。 它是与所有应用程序和连接进行交互的前端。 MPP 引擎在控制节点上运行以优化和协调并行查询。 将 T-SQL 查询提交到 SQL 数据仓库时，控制节点会将其转换为可针对每个分布区并行运行的查询。

### <a name="compute-nodes"></a>计算节点

计算节点提供计算能力。 分布区映射到计算节点以进行处理。 如果支付更多计算资源费用，SQL 数据仓库会将分布区重新映射到可用的计算节点。 计算节点数的范围是 1 到 60，它由数据仓库的服务级别确定。

每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](http://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)。

### <a name="data-movement-service"></a>数据移动服务
数据移动服务 (DMS) 是一项数据传输技术，它可协调计算节点间的数据移动。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。 

## <a name="distributions"></a>分发

分布区是存储和处理针对分布式数据运行的并行查询的基本单位。 SQL 数据仓库运行查询时，工作会被分割成 60 个并行运行的小型查询。 每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最多计算资源的数据仓库的每个分布区占 1 个计算节点。 具有最少计算资源的数据仓库的所有分布区都在 1 个计算节点上。  

## <a name="hash-distributed-tables"></a>哈希分布表
哈希分布表可为大型表上的联接和聚合提供最高查询性能。 

为了将数据分片到哈希分布式表中，SQL 数据仓库使用哈希函数明确将 1 个行分配到 1 个分布区。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。 

![分布式表](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分布式表")  

* 一个行属于一个分布区。  
* 通过确定性哈希算法将一个行分配到一个分布区。  
* 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表
轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 轮循机制表上的联接要求重新安排数据，因此这需要花费更多时间。

## <a name="replicated-tables"></a>复制表
复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 它需要额外存储并且在写入数据时会产生额外负载，因此不适用于大型表。  

下图显示了一个复制表。 在 SQL 数据仓库中，会在每个计算节点的第一个分布区上缓存复制表。  

![Replicated table](media/sql-data-warehouse-distributed-data/replicated-table.png "Replicated table") 

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请继续了解如何快速[创建 SQL 数据仓库][创建 SQL 数据仓库]和[加载示例数据][加载示例数据]。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表][Azure 词汇表]可以提供帮助。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

<!-- Not Available * [Customer success stories] -->
<!-- Not Available * [Blogs]-->
* [功能请求]
<!-- Not Available * [Videos]-->
<!-- Not Available * [Customer Advisory Team blogs]-->
<!-- Not Available * [Create support ticket]-->
<!-- Not Available * [MSDN forum]-->
<!-- Not Available * [Stack Overflow forum]-->
<!-- Not Available * [Twitter] -->

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
<!-- Not Available [Create support ticket]:./sql-data-warehouse-get-started-create-support-ticket.md-->
[加载示例数据]: ./sql-data-warehouse-load-sample-databases.md [创建 SQL 数据仓库]: ./sql-data-warehouse-get-started-provision.md [迁移文档]: ./sql-data-warehouse-overview-migrate.md
<!-- Not Avaialble on [SQL Data Warehouse solution partners]:./sql-data-warehouse-partner-business-intelligence.md -->
[集成工具概述]: ./sql-data-warehouse-overview-integrate.md [备份和还原概述]: ./sql-data-warehouse-restore-database-overview.md [Azure 词汇表]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[功能请求]: https://support.windowsazure.cn/support/support-azure

[SLA for SQL Data Warehouse]: https://www.azure.cn/support/sla/sql-data-warehouse/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://www.azure.cn/support/legal/sla/
<!-- Update_Description: new articles on massively parallel processing of mpp architecture -->