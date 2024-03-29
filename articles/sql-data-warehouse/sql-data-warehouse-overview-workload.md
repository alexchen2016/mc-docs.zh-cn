---
title: 了解 Azure SQL 数据仓库操作 | Microsoft Docs
description: '借助 SQL 数据仓库的弹性，可以使用数据仓库单位 (DWU) 的可调缩放性扩大、收缩或暂停提供计算能力。 本文介绍数据仓库指标以及它们如何与 DWU 相关。 '
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
origin.date: 04/17/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: a65a1019f3d2a358666030c3a658dee0d1c095ab
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52651358"
---
# <a name="data-warehouse-workload"></a>数据仓库工作负荷
数据仓库工作负荷是指所有针对数据仓库所发生的操作。 数据仓库工作负荷包括将数据载入仓库、对数据仓库执行分析和报告、管理数据仓库中的数据，以及从数据仓库导出数据的整个过程。 这些组件的深度与广度多半与数据仓库的成熟度相当。

## <a name="new-to-data-warehousing"></a>不熟悉数据仓库？
数据仓库是从一个或多个数据源加载的数据集合，用于执行报告和数据分析等商业智能任务。

数据仓库的特点在于查询可以扫描数量较大的行和大范围的数据，还可以返回相对较大的结果用于分析和报告目的。 与小规模事务级插入/更新/删除操作相比，可加载相对较大的数据也是数据仓库的特点。

* 如果以扫描大量行或大范围数据所需的优化查询方式存储数据，数据仓库便能提供最佳性能。 如果按列（而不是行）存储和搜索数据，这种扫描类型可以提供最佳效果。

> [!NOTE]
> 与用于报告和分析查询的传统二进制树相比，使用列存储的内存中列存储索引最大可将压缩量提升 10 倍，将查询性能提升 100 倍。 我们将列存储索引视为在数据仓库中存储和扫描大量数据的标准。
> 
> 

* 数据仓库与针对联机事务处理 (OLTP) 进行优化的系统存在不同的要求。 OLTP 系统有许多插入、更新和删除操作。 这些操作搜索表中的特定行。 如果数据以逐行方式存储，表搜索便能提供最佳性能。 可以使用分治法（称为二进制树或 btree 搜索）排序和快速搜索数据。

## <a name="data-loading"></a>数据加载
数据仓库工作负荷大部分都是在加载数据。 企业的 OLTP 系统通常忙于跟踪客户一整天下来生成的业务事务更改。 通常在夜间进行的维护操作定期将事务移动或复制到数据仓库。 数据进入数据仓库后，分析人员可以执行分析，并根据数据做出业务决策。

* 传统上，加载过程称为 ETL，即提取、转换和加载。 数据通常需要进行转换才能与数据仓库中的其他数据保持一致。 以前，企业使用专用的 ETL 服务器执行转换。 现在，如果要进行如此快速的大规模并行处理，用户可以先将数据载入 SQL 数据仓库，然后再执行转换。 此过程称为提取、加载再转换 (ELT)，正逐渐成为数据仓库工作负荷的全新标准。

> [!NOTE]
> 现在，使用 SQL Server 2016 就能对 OLTP 表进行实时分析。 尽管这不会取代数据仓库存储和分析数据的需求，但确实为执行实时分析提供了一种方式。
> 
> 

### <a name="reporting-and-analysis-queries"></a>报告和分析查询
报告和分析查询通常根据几个条件分成小型、中型和大型，但通常是根据时间。 在大多数数据仓库中，还有快速运行与长时间运行查询的混合工作负荷。 在每种情况下，必须确定此混合方式以及它的频率（每小时、每天、月末、季末等等）。 请务必了解混合查询工作负荷配合并发性可为数据仓库提供适当的容量计划。

* 对于混合查询工作负荷而言，容量计划是一项复杂的任务，尤其是当需要很长的前置时间将容量增加到数据仓库时。 SQL 数据仓库可让容量计划不再急迫，因为可以随时增加或缩减计算容量，并且能够独立地调整存储和计算容量。

### <a name="data-management"></a>数据管理
管理数据相当重要，尤其是知道不久之后可能用尽磁盘空间时。 数据仓库通常将数据分割成有意义的范围，并存储为表中的分区。 所有基于 SQL Server 的产品都允许你将分区移入和移出表。 这种分区切换可让你将较旧的数据移到较便宜的存储，并将最新的数据保留在联机存储中。

* 列存储索引支持分区表。 列存储索引使用分区表管理和存档数据。 对于逐行存储的表而言，分区在查询性能方面承担重要的角色。  
* PolyBase 在管理数据方面扮演着重要角色。 使用 PolyBase，可以选择将较旧的数据存档在 Hadoop 或 Azure Blob 存储中。  由于数据仍保持联机，因此可以提供大量选项。  从 Hadoop 检索数据可能需要较长的时间，但以检索时间换取存储成本，可能会得不偿失。

### <a name="exporting-data"></a>导出数据
使数据可用于报告和分析的一种方法是将数据从数据仓库发送到专门用于运行报告和分析的服务器。 这些服务器称为数据市场。 例如，用户可以预先处理报告数据，并将它从数据仓库导出到全球各地的许多服务器中，以提供给各地的客户和分析人员使用。

* 可以在每天晚上将每天的数据快照填入只读的报告服务器以生成报告。 这样可为客户提供较高的带宽，同时降低数据仓库的计算资源需求。 从安全性角度来看，数据市场可以减少访问数据仓库的用户数。
* 如果要执行分析，可以在数据仓库中生成分析多维数据集，并针对数据仓库运行分析，或是预先处理数据，再导出到分析服务器做进一步的分析。

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请继续学习如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]和[加载示例数据][load sample data]。

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->