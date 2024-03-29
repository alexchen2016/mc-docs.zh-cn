---
title: Azure Analysis Services 教程第 7 课：创建关键绩效指标 | Azure
description: 介绍如何在 Azure Analysis Services 教程项目中创建关键绩效指标。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: b07ccadef1148d3250b16edb9215a90f20b99078
ms.sourcegitcommit: b24f0712fbf21eadf515481f0fa219bbba08bd0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55085641"
---
# <a name="create-key-performance-indicators"></a>创建关键绩效指标

在本课中，将创建关键绩效指标 (KPI)。 KPI 用于衡量由基本度量值定义的某个值的绩效（与同样由度量值或绝对值定义的目标值进行对比）。 在报告客户端应用程序中，KPI 可以为业务专业人员提供一种快速简单的方法来了解业务成功摘要情况或查明趋势。 若要了解详细信息，请参阅 [KPI](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)

本课预计完成时间：**15 分钟**  

## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 6 课：创建度量值](../tutorials/aas-lesson-6-create-measures.md)。   

## <a name="create-key-performance-indicators"></a>创建关键绩效指标  

#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>创建 InternetCurrentQuarterSalesPerformance KPI  

1.  在模型设计器中，单击“FactInternetSales”表。  

2.  在度量值网格中，单击某个空单元格。  

3.  在表上方的公式栏中，键入以下公式： 

    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    此度量值用作 KPI 的基本度量值。  

4.  在度量值网格中，右键单击“InternetCurrentQuarterSalesPerformance” > “创建 KPI”。   

5.  在“关键绩效指标 (KPI)”对话框中，在“目标”中选择“绝对值”，并键入“1.1”。  

7.  在左侧的（下限）滑块字段中，键入“1”，并在右侧的（上限）滑块字段中键入“1.07”。  

8.  在“选择图标样式”中，选择菱形（红色）、三角形（黄色）、圆形（绿色）图标类型。

    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)

    > [!TIP]  
    > 请注意可用图标样式下方的“说明”标签。 为各种 KPI 元素使用不同说明，使其在客户端应用程序中更容易识别。  

9. 单击“确定”以完成 KPI。  

    在度量值网格中，请注意“InternetCurrentQuarterSalesPerformance”度量值旁边的图标。 此图标指示此度量值用作某个 KPI 的基值。  

#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>创建 InternetCurrentQuarterMarginPerformance KPI  

1.  在“FactInternetSales”表的度量值网格中，单击某个空单元格。  

2.  在表上方的公式栏中，键入以下公式：  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```

3.  右键单击“InternetCurrentQuarterMarginPerformance”，并单击“创建 KPI”。 >   

4.  在“关键绩效指标 (KPI)”对话框中，在“目标”中选择“绝对值”，并键入“1.25”。   

5.  在左侧的（下限）滑块字段中，滑动至此字段显示“0.8”，并滑动右侧的（上限）滑块字段，直至此字段显示“1.03”。  

6.  在“选择图标样式”中，选择菱形（红色）、三角形（黄色）、圆形（绿色）图标类型，并单击“确定”。  

## <a name="whats-next"></a>后续步骤
[第 8 课：创建透视](../tutorials/aas-lesson-8-create-perspectives.md)。

<!--Update_Description: update meta properties  -->