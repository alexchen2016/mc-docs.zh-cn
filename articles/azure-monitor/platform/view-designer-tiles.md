---
title: Azure Monitor 中视图设计器磁贴的参考指南 | Azure Docs
description: 可以通过 Azure Monitor 中的视图设计器创建自定义视图，此类视图在 Azure 门户中显示，包含 Log Analytics 工作区中的多种基于数据的可视化效果。 本文针对自定义视图中可用的磁贴，提供设置方面的参考指南。
services: log-analytics
documentationcenter: ''
author: lingliw
manager: digimobile
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 6/4/2019
ms.author: v-lingwu
ms.openlocfilehash: 5063b9bb792428f8f99ff56c174e6aac6438070b
ms.sourcegitcommit: f818003595bd7a6aa66b0d3e1e0e92e79b059868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66731416"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Azure Monitor 中视图设计器磁贴的参考指南
在 Azure Monitor 中使用视图设计器即可在 Azure 门户中创建各种自定义视图，使 Log Analytics 工作区中的数据可视化。 本文针对自定义视图中可用的磁贴，提供设置方面的参考指南。

有关视图设计器的详细信息，请参阅：

* [视图设计器](view-designer.md)：概述视图设计器以及创建和编辑自定义视图的过程。
* [可视化部分参考](view-designer-parts.md)：针对自定义视图中可用的可视化部件，提供设置方面的参考指南。


下表描述了可用的视图设计器磁贴：  

| 磁贴 | 说明 |
|:--- |:--- |
| [数字](#number-tile) |查询中的记录数。 |
| [两个数字](#two-numbers-tile) |两个不同查询中的记录数。 |
| [圆环图](#donut-tile) | 基于一个查询的图表，中心显示汇总值。 |
| 折线图和标注 | 基于一个查询的折线图，并使用汇总值进行标注。 |
| [折线图](#line-chart-tile) |基于一个查询的折线图。 |
| [两条时间线](#two-timelines-tile) | 具有两个序列的柱状图，每个序列基于一个单独的查询。 |

后续部分将详细介绍磁贴类型及其属性。

> [!NOTE]
> 视图中的磁贴基于 Log Analytics 工作区中的[日志查询](../log-query/log-query-overview.md)。 它们目前不支持使用[跨资源查询](../log-query/cross-workspace-query.md)从 Application Insights 检索数据。

## <a name="number-tile"></a>数字磁贴
**数字**磁贴显示一个日志查询中的记录数，具有一个标签。

![数字磁贴](media/view-designer-tiles/tile-number.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| **磁贴** | |
| 图例 |在值下面显示的文本。 |
| 查询 |运行的查询。 显示由查询返回的记录数。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="two-numbers-tile"></a>两个数字磁贴
此磁贴显示两个不同日志查询中的记录数，每个查询具有一个标签。

![两个数字磁贴](media/view-designer-tiles/tile-two-numbers.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| **第一个磁贴** | |
| 图例 |在值下面显示的文本。 |
| 查询 |运行的查询。 显示由查询返回的记录数。 |
| **第二个磁贴** | |
| 图例 |在值下面显示的文本。 |
| 查询 |运行的查询。 显示由查询返回的记录数。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="donut-tile"></a>圆环图磁贴
**圆环图**磁贴显示从一个日志查询中的值列汇总的单个数字。 此圆环图以图形方式显示了前三个记录的结果。

![圆环图磁贴](media/view-designer-tiles/tile-donut.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| **圆环图** | |
| 查询 |要为该圆环图运行的查询。 第一个属性应为文本值，第二个属性应为数值。 此查询通常使用 *measure* 关键字来汇总结果。 |
| **圆环图** |**> 中心** |
| 文本 |在该圆环图中的值下显示的文本。 |
| 操作 |要在值属性上执行以汇总到单个值的操作。<ul><li>总和：累加具有该属性值的所有记录的值。</li><li>百分比：具有该属性值的记录的总和值占所有记录总和值的百分比。</li></ul> |
| 用于中心操作的结果值 |（可选）选择加号 (+) 可添加一个或多个值。 查询的结果将限于具有指定属性值的记录。 如果没有添加任何值，则查询中包含所有记录。 |
| **圆环图** |**>其他选项** |
| 颜色 |为前三个主要属性分别显示的颜色。 若要特定属性值指定备选颜色，可使用“高级颜色映射”  。 |
| 高级颜色映射 |显示表示特定属性值的颜色。 如果指定的值位于前三，则会显示备选颜色而不是标准颜色。 如果该属性不在前三，则不会显示颜色。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="line-chart-tile"></a>折线图磁贴
此磁贴是一个折线图，显示一段时间内一个日志查询中的多个系列。 

![折线图和标注磁贴](media/view-designer-tiles/tile-line-chart.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| **折线图** | |
| 查询 |要为该折线图运行的查询。 第一个属性应为文本值，第二个属性应为数值。 此查询通常使用 *measure* 关键字来汇总结果。 如果查询使用 *interval* 关键字，则 X 轴将使用此时间间隔。 如果查询不使用 *interval* 关键字，则 X 轴使用每小时间隔。 |
| **折线图** |**> Y 轴** |
| 使用对数刻度 |选择此链接可对 Y 轴使用对数刻度。 |
| Units |指定由查询返回的值的单位。 此信息用于显示在图表上指示值类型的标签，并可用于转换值。 “**单位类型**”指定单位的类别，并定义“**当前单位类型**”可用的值。 如果选择一个位于“转换为”  中的值，则该数字值将从“当前单位”  类型转换为“转换为”  类型。 |
| 自定义标签 |要在 Y 轴上“单位”类型标签旁边显示的文本  。 如果未指定任何标签，则将仅显示“单位”类型  。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="line-chart-and-callout-tile"></a>折线图和标注磁贴
此磁贴包含一个折线图，其中显示了一段时间内来自一个日志查询中的具有多个系列；另外还包含一个标注，其中显示了汇总值。 

![折线图和标注磁贴](media/view-designer-tiles/tile-line-chart-callout.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| **折线图** | |
| 查询 |要为该折线图运行的查询。 第一个属性应为文本值，第二个属性应为数值。 此查询通常使用 *measure* 关键字来汇总结果。 如果查询使用 *interval* 关键字，则 X 轴将使用此时间间隔。 如果查询不使用 *interval* 关键字，则 X 轴使用每小时间隔。 |
| **折线图** |**> 标注** |
| 标注标题 | 标注值上方显示的文本。 |
| 序列名称 |用作标注值的序列属性值。 如果没有提供序列，则将使用查询中的所有记录。 |
| 操作 |要在值属性上执行以汇总到单个标注值的操作。<ul><li>平均值：所有记录中的值的平均值。</li><li>计数：由查询返回的所有记录的计数。</li><li>上一次采样：图表中包含的最后一个时间间隔的值。</li><li>最大值：图表中包含的时间间隔的最大值。</li><li>最小值：图表中包含的时间间隔的最小值。</li><li>总和：所有记录中的值的总和。</li></ul> |
| **折线图** |**> Y 轴** |
| 使用对数刻度 |选择此链接可对 Y 轴使用对数刻度。 |
| Units |指定由查询返回的值的单位。 此信息用于显示在图表上指示值类型的标签，并可用于转换值。 “单位类型”指定单位的类别，并定义可用的“当前单位类型”值   。 如果选择一个位于“转换为”  中的值，则该数字值将从“当前单位”  类型转换为“转换为”  类型。 |
| 自定义标签 |要在 Y 轴上“单位”类型标签旁边显示的文本  。 如果未指定任何标签，则将仅显示“单位”类型  。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="two-timelines-tile"></a>两个时间线磁贴
**两个时间线**磁贴以柱形图显示一段时间内的两个日志查询的结果。 为每个序列显示一个标注。 

![两个时间线磁贴](media/view-designer-tiles/tile-two-timelines.png)

| 设置 | 说明 |
|:--- |:--- |
| Name |在磁贴顶部显示的文本。 |
| 说明 |在磁贴名称下面显示的文本。 |
| 第一个图表 | |
| 图例 |在第一个序列的标注下显示的文本。 |
| 颜色 |用于第一个序列中的列的颜色。 |
| 图表查询 |针对第一个序列运行的查询。 每个时间间隔内记录数由图表列表示。 |
| 操作 |要在值属性上执行以汇总到单个标注值的操作。<ul><li>平均值：所有记录中的值的平均值。</li><li>计数：由查询返回的所有记录的计数。</li><li>上一次采样：图表中包含的最后一个时间间隔的值。</li><li>最大值：图表中包含的时间间隔的最大值。</li></ul> |
| **第二个图表** | |
| 图例 |在第二个序列的标注下显示的文本。 |
| 颜色 |用于第二个序列中的列的颜色。 |
| 图表查询 |针对第二个序列运行的查询。 每个时间间隔内记录数由图表列表示。 |
| 操作 |要在值属性上执行以汇总到单个标注值的操作。<ul><li>平均值：所有记录中的值的平均值。</li><li>计数：由查询返回的所有记录的计数。</li><li>上一次采样：图表中包含的最后一个时间间隔的值。</li><li>最大值：图表中包含的时间间隔的最大值。 |
| **高级** |**> 数据流验证** |
| Enabled |如果应为磁贴启用数据流验证，请选择此链接。 如果数据不可用，此方法将提供一条替代消息。 通常使用此方法在安装视图后数据变为可用状态的短暂过程中提供一条消息。 |
| 查询 |要运行的查询，用于确定该视图是否有可用数据。 如果查询无结果返回，则会显示一条消息而不是主查询中的值。 |
| Message |数据流验证查询无数据返回时显示的消息。 如果没有提供任何消息，则会显示“正在执行评估”状态消息  。 |


## <a name="next-steps"></a>后续步骤
* 了解有关[日志查询](../log-query/log-query-overview.md)以支持磁贴中的查询。
* 将[可视化部件](view-designer-parts.md)添加到自定义视图。




