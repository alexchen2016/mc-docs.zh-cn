---
title: Azure Analysis Services 教程第 4 课：创建关系 | Azure
description: 介绍如何在 Azure Analysis Services 教程项目中创建关系。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: 0a57b4be4720a6f6b3174274a5ea437fe9e5ba0e
ms.sourcegitcommit: b24f0712fbf21eadf515481f0fa219bbba08bd0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55085652"
---
# <a name="create-relationships"></a>创建关系

本课程介绍如何验证导入数据时自动创建的关系，以及在不同的表之间添加新关系。 关系是两个表之间的连接，规定如何关联这些表中的数据。 例如，在 DimProduct 表与 DimProductSubcategory 表之间，基于每种产品属于某个子类别这一事实建立了关系。 若要了解详细信息，请参阅[关系](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular)。

本课预计完成时间：**10 分钟**  

## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 3 课：标记为日期表](../tutorials/aas-lesson-3-mark-as-date-table.md)。 

## <a name="review-existing-relationships-and-add-new-relationships"></a>查看现有关系和添加新关系  
使用“获取数据”导入数据时，可以从 AdventureWorksDW2014 数据库获取 7 个表。 一般情况下，从关系型源导入数据时，现有关系会连同数据一起自动导入。 若要通过“获取数据”自动在数据模型中创建关系，必须在数据源的表之间存在关系。

在继续创作模型之前，应验证是否已正确创建表之间的这些关系。 在本教程中，还需添加三个新关系。  

#### <a name="to-review-existing-relationships"></a>查看现有关系  

1.  单击“模型”菜单 >“模型视图” > “关系图视图”。  

    现在，模型设计器将以关系图视图出现，这是一种图形格式，显示已导入的所有表，且这些表之间以线条连接。 表之间的线条表示导入数据时自动创建的关系。

    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)

    > [!NOTE]
    > 如果看不到表之间存在任何关系，则可能意味着数据源的这些表之间没有关系。

    使用模型设计器右下角的迷你图控件，尽可能包含最多的表。 还可以单击表并将其拖放到不同的位置，使表更加相互靠近，或者按特定的顺序排列它们。 移动表不会影响表之间的关系。 若要查看特定表中的所有列，请单击并拖动表的边缘，扩大或缩小该表。  

2.  单击 **DimCustomer** 表与 **DimGeography** 表之间的实线。 这两个表之间的实线表明这种关系处于活动状态，也就是说，在计算 DAX 公式时会默认使用此关系。  

    请注意，**DimCustomer** 表中的 **GeographyKey** 列和 **DimGeography** 表中的 **GeographyKey** 列现在各显示在一个框中。 关系中需使用这些列。 关系的属性现在也显示在“属性”窗口中。  

    > [!TIP]  
    > 除了在关系图视图中使用模型设计器以外，还可以使用“管理关系”对话框以表格格式显示所有表之间的关系。 在表格模型资源管理器中，右键单击“关系” > “管理关系”。

3.  验证从 AdventureWorksDW 数据库导入每个表时，创建了以下关系：  

    |活动|表|相关的查找表|  
    |----------|---------|------------------------|  
    |是|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |是|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |是|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |是|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |是|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  

    如果缺少任意关系，请验证模型是否包含以下表：DimCustomer、DimDate、DimGeography、DimProduct、DimProductCategory、DimProductSubcategory 和 FactInternetSales。 如果在不同时间导入同一数据源连接中的表，则无法创建这些表之间的任何关系，而必须手动创建。 如果没有关系出现，这意味着在数据源中没有任何关系。 可以在数据模型中手动创建它们。

### <a name="take-a-closer-look"></a>详细查看
在图示视图中，可看到一个箭头、一个星号，以及显示表之间关系的线条上的数字。

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

箭头显示筛选器方向。 星号显示此表是关系基数中的“多”端，1 显示此表是关系中的“一”端。 如果需要编辑某个关系（例如，更改关系的筛选方向或基数），请双击关系线条打开“编辑关系”对话框。

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

这些功能用于高级数据建模，不在本教程的范畴内。 若要了解详细信息，请参阅 [Analysis Services 中表格模型的双向交叉筛选器](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services)。

在某些情况下，可能需要在模型中的表之间创建更多关系，以支持特定的业务逻辑。 对于本教程，需要在 FactInternetSales 表与 DimDate 表之间创建 3 个额外的关系。  

#### <a name="to-add-new-relationships-between-tables"></a>在表之间添加新关系  

1.  在模型设计器中的 **FactInternetSales** 表内，单击并按住 **OrderDate** 列，将光标拖到 **DimDate** 表中的 **Date** 列，并松开鼠标。  

    此时会显示一条实线，表明已在 **Internet Sales** 表中的 **OrderDate** 列与 **Date** 表中的 **Date** 列之间创建活动的关系。 

      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 

    > [!NOTE]  
    > 创建关系时，会自动选择主表与相关查找表之间的基数和筛选方向。  

2.  在 **FactInternetSales** 表中，单击并按住 **DueDate** 列，将光标拖到 **DimDate** 表中的 **Date** 列，并松开鼠标。  

    此时会显示虚线，表明已在 **FactInternetSales** 表中的 **DueDate** 列与 **DimDate** 表中的 **Date** 列之间创建非活动的关系。 可以在表之间创建多个关系，但每次只能有一个关系处于活动状态。 可将非活动关系设为活动状态，以便在自定义 DAX 表达式中执行特殊聚合。  

3.  最后，再创建一个关系。 在 **FactInternetSales** 表中，单击并按住 **ShipDate** 列，将光标拖到 **DimDate** 表中的 **Date** 列，并松开鼠标。  

     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)

## <a name="whats-next"></a>后续步骤
[第 5 课：创建计算列](../tutorials/aas-lesson-5-create-calculated-columns.md)。

<!--Update_Description: update meta properties, wording update  -->