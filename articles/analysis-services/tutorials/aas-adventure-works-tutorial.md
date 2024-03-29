---
title: Azure Analysis Services Adventure Works 教程 | Azure
description: 介绍 Azure Analysis Services 的 Adventure Works 教程
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3031ef819896c20efedf30c4acc59dba42360
ms.sourcegitcommit: b24f0712fbf21eadf515481f0fa219bbba08bd0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55085634"
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services - Adventure Works 教程

本教程提供的课程介绍了如何使用 Visual Studio [Analysis Services 项目](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects)或 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)，在 1400 兼容级别创建和部署表格模型。  
如果不熟悉 Analysis Services 和表格建模，则完成本教程是了解如何使用 Visual Studio 创建和部署基本表格模型的最快方法。 在满足所有先决条件后，需花费两到三个小时来完成本教程。  

## <a name="what-you-learn"></a>学习内容   

-   如何在 Visual Studio 中以 **1400 兼容级别**创建新的表格模型项目。

-   如何将数据从关系数据库导入到表格模型项目工作区数据库中。  

-   如何创建和管理模型中的表之间的关系。  

-   如何创建可帮助用户分析关键业务指标的计算列、度量值和关键绩效指标。  

-   如何通过提供特定于业务和应用程序的视点创建和管理可帮助用户更轻松地浏览模型数据的透视和层次结构。  

-   如何创建分区将表数据划分为较小可处理的逻辑部分独立于其他分区。  

-   如何通过创建包含用户成员的角色来保护模型对象和数据的安全。  

-   如何使用 Visual Studio 将表格模型部署到 **Azure Analysis Services** 服务器或 **SQL Server 2017 Analysis Services** 服务器。  

## <a name="prerequisites"></a>先决条件  
要完成本教程，需要：  

-   Azure Analysis Services 服务器。 注册免费的 [Azure Analysis Services 试用版](https://www.azure.cn/home/features/analysis-services/)并[创建服务器](../analysis-services-create-server.md)。 

-   包含**示例 AdventureWorksDW 数据库**的 [Azure SQL 数据仓库](../../sql-data-warehouse/create-data-warehouse-portal.md)，或者包含 [Adventure Works 示例数据库](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)的 SQL Server 数据仓库。

    **重要提示：** 如果在本地 SQL Server 数据仓库上安装示例数据库，并且将模型部署到 Azure Analysis Services 服务器，则需要[本地数据网关](../analysis-services-gateway.md)。

-   最新版本的用于 Visual Studio 的 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)。

-   最新版本的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。    

-   客户端应用程序，例如 [Power BI Desktop](https://powerbi.microsoft.com/desktop/) 或 Excel。 

## <a name="scenario"></a>方案  
本教程基于 Adventure Works Cycles（一家虚构的公司）。 Adventure Works 是一家大型的跨国制造公司，生产自行车及其零配件并面向北美、欧洲和亚洲的商业市场销售。 公司雇佣了 500 名员工。 另外，Adventure Works 还雇用了遍布其市场群的多个区域销售团队。 项目内容是为销售和营销用户创建表格模型来分析 AdventureWorksDW 数据库中的 Internet 销售数据。  

若要完成本教程，必须完成多个课程。 在每个课程中，需执行一些任务。 若要完成本课程，必须按序完成每个任务。 特定课程中可能有几个任务会实现类似的结果，但完成每个任务的方式略有不同。 此方法说明通常可通过多种方法来完成某个任务，并提供机会练习使用在之前的课程和任务中学习的技能。  

各个课程的目的是指导使用 SSDT 中包括的许多功能创作基本的表格模型。 因为每一课程都以上一课程为基础，因此必须按顺序完成各个课程。

本教程不提供有关后列内容的课程：在 Azure 门户中管理服务器、通过使用 SSMS 或通过使用客户端应用程序浏览模型数据来管理服务器或数据库。 

## <a name="lessons"></a>课程  
本教程包括以下课程：  

|课程|估计完成时间|  
|----------|------------------------------|  
|[1 - 创建新的表格模型项目](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 分钟|  
|[2 - 获取数据](../tutorials/aas-lesson-2-get-data.md)|10 分钟|  
|[3 - 标记为日期表](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 分钟|  
|[4 - 创建关系](../tutorials/aas-lesson-4-create-relationships.md)|10 分钟|  
|[5 - 创建计算列](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 分钟|
|[6 - 创建度量值](../tutorials/aas-lesson-6-create-measures.md)|30 分钟|  
|[7 - 创建关键绩效指标 (KPI)](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 分钟|  
|[8 - 创建透视](../tutorials/aas-lesson-8-create-perspectives.md)|5 分钟|  
|[9 - 创建层次结构](../tutorials/aas-lesson-9-create-hierarchies.md)|20 分钟|  
|[10 - 创建分区](../tutorials/aas-lesson-10-create-partitions.md)|15 分钟|  
|[11 - 创建角色](../tutorials/aas-lesson-11-create-roles.md)|15 分钟|  
|[12 - 在 Excel 中分析](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 分钟| 
|[13 - 部署](../tutorials/aas-lesson-13-deploy.md)|5 分钟|  

## <a name="supplemental-lessons"></a>补充课程  
这些课程不是完成本教程所必需的，但有助于更好地理解高级表格模型创作功能。  

|课程|估计完成时间|  
|----------|------------------------------|  
|[详细信息行](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 分钟|
|[动态安全性](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 分钟|
|[不规则层次结构](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 分钟| 

## <a name="next-steps"></a>后续步骤  
若要入门，请参阅[第 1 课：创建新的表格模型项目](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)。

<!--Update_Description: update meta properties -->
