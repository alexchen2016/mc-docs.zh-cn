---
title: Azure Analysis Services 教程第 9 课：创建层次结构 | Azure
description: 介绍如何在表格模型中创建层次结构。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 04/15/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: cf3fcfc7f35a7bfe82ae818fddbb3190671a782b
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529336"
---
# <a name="create-hierarchies"></a>创建层次结构

在本课中，将创建层次结构。 层次结构是一组组的列，按级别排列。 例如，“地理区域”层次结构可包含“国家/地区”、“省/市/自治区”、“县”和“城市”子级别。 层次结构在报告客户端应用程序字段列表中可以与其他列分开显示，这使得客户端用户更容易在其中导航以及将其包括在报表中。 若要了解详细信息，请参阅[层次结构](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)

若要创建层次结构，请在“关系图视图”中使用模型设计器。 “数据视图”中不支持创建和管理层次结构。  

本课预计完成时间：**20 分钟**  

## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 8 课：创建透视](../tutorials/aas-lesson-8-create-perspectives.md)。  

## <a name="create-hierarchies"></a>创建层次结构  

#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>在 DimProduct 表中创建 Category 层次结构  

1.  在模型设计器（关系图视图）中，右键单击“DimProduct”表，并单击“创建层次结构”。 一个新的层次结构将出现在表窗口的底部。 将该层次结构重命名为“Category”。  

2.  单击“ProductCategoryName”列并将其拖动到新的“Category”层次结构。  

3.  在“Category”层次结构中，右键单击“ProductCategoryName”，再单击“重命名”，然后键入“Category”。 >   

    > [!NOTE]  
    > 重命名层次结构中的某个列不会重命名表中的该列。 层次结构中的列只是表中的该列的一种表示形式。  

4.  单击“ProductSubcategoryName”列并将其拖动到“Category”层次结构。 将其重命名为“Subcategory”。 

5.  右键单击“ModelName”列，单击“添加到层次结构”，然后选择“Category”。 将其重命名为“Model”。

6.  最后，将“EnglishProductName”添加到 Category 层次结构。 将其重命名为“Product”。  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)

#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>在 DimDate 表中创建层次结构  

1. 在 DimDate 表中，创建一个名为“Calendar”的层次结构。  

2. 按顺序添加以下列：

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth

3. 在“DimDate”表中，创建“Fiscal”层次结构。 按顺序包括以下列：  

    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth

4. 最后，在“DimDate”表中，创建“ProductionCalendar”层次结构。 按顺序包括以下列：  
    
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek

## <a name="whats-next"></a>后续步骤
[第 10 课：创建分区](../tutorials/aas-lesson-10-create-partitions.md)。

<!--Update_Description: update meta properties -->