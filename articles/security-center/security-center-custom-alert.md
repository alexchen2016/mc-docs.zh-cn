---
title: Azure 安全中心内的自定义警报规则 | Azure Docs
description: 本文档介绍了如何在 Azure 安全中心创建自定义警报规则。
services: security-center
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2019
ms.author: v-lingwu
ms.openlocfilehash: 8815c1f9bc41c6f053dc1f7474d1ec9217a7a377
ms.sourcegitcommit: e77582e79df32272e64c6765fdb3613241671c20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135987"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Azure 安全中心的自定义警报规则（预览版）
本文档介绍了如何在 Azure 安全中心创建自定义警报规则。

> [!NOTE]
> 自定义警报将在 2019 年 6 月 30 日停用。

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>停用 Azure 安全中心内的自定义警报规则

自定义警报体验将在 2019 年 6 月 30 日停用，因为它所基于的底层基础结构已停用。 在弃用此体验之前的时段内，用户可以编辑现有的自定义警报规则，但无法添加新的自定义警报规则。
建议用户：
- 使用一键式加入功能来启用 [Azure Sentinel](https://www.azure.cn/services/azure-sentinel/)，以自动迁移其现有警报和创建新的警报
- 使用 Azure Monitor 日志警报重新创建警报
                                     
若要保留现有的警报并将其迁移到 Azure Sentinel，请[启动 Azure Sentinel](https://portal.azure.cn/#create/Microsoft.ASI/preview)。 第一步是选择存储自定义警报的工作区，然后选择“分析”菜单项以自动迁移警报。

> [!NOTE]
> 将自定义警报迁移到 Azure Sentinel 会一次性迁移所选工作区中的所有自定义警报。 完成迁移后，无法通过 Azure 安全中心访问该所选工作区的自定义警报。
>
> 使用 [Search](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) 或 [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) 语句查询的自定义警报在 Azure Sentinel 中不受支持，且不会迁移。 在执行迁移之前，请编辑这些警报。

若要使用 Azure Monitor 日志警报重新创建警报，请参阅：[使用 Azure Monitor 创建、查看和管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，获取有关如何创建日志警报的说明。 有关 Azure Monitor 中日志警报的一般概述，请单击[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)。

## <a name="what-are-custom-alert-rules-in-security-center"></a>安全中心的自定义警报规则是什么？

安全中心有一组预定义的[安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)，这些警报在出现威胁或可疑活动时触发。 在某些情况下，可能要根据环境的具体需求创建自定义警报。

可以使用安全中心的自定义警报规则，根据从环境中收集到的数据定义新的安全警报。 可以先创建查询，再将这些查询的结果用作自定义规则的条件，在符合该条件的情况下执行规则。 可以使用计算机安全事件、合作伙伴的安全解决方案日志或者通过 API 引入的数据来创建自定义查询。

> [!NOTE]
> 安全中心的[调查功能](security-center-investigation.md)不支持自定义警报。
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>如何在安全中心创建自定义警报规则？

打开“安全中心”  仪表板，按以下步骤创建自定义警报规则：

1.  在左窗格的“检测”下单击“自定义警报规则(预览版)”。  
2.  在“安全中心 - 自定义警报规则(预览版)”页中单击“新建自定义警报规则”。  

    ![自定义警报](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  此时会显示“创建自定义警报规则”页，其中包含以下选项：

    ![创建](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  在“名称”  字段中，键入此自定义规则的名称。
5.  在“说明”字段中根据该规则的目的键入简要说明。 
6.  在“严重性”字段中根据需要选择严重级别（高、中、低）。 
7.  在“订阅”字段中选择此规则适用的订阅。 
8.  在“工作区”字段中选择要使用此规则进行监视的工作区，并在“搜索查询”字段中选择要用于获取结果的查询。  

    > [!NOTE]
    > 需要在选择用来存储自定义警报的工作区中具有写入权限。
    >
    >

    查询的结果触发警报。 请注意，键入有效查询时，会在以下字段的右角显示绿色复选标记：

    ![查询](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. 在“期间”字段中选择执行上述查询的时间范围。  请注意，此字段底部的搜索结果会随所选时间范围而变。

    ![周期](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. 在“评估”字段中，选择评估和执行此规则的频率。 
12. 在“结果数”字段中，选择运算符（大于或小于）。 
13. 在“阈值”字段中键入一个数字，该数字将用作此前所选运算符的参考。 
14. 如果需要设置一个等待时间，在该时间过后才通过安全中心发送该规则的另一警报，则启用“阻止警报”选项。 
15. 单击“确定”  完成。

创建完新的警报规则以后，该规则会显示在自定义警报规则列表中。 如果符合该规则的条件，则会触发新的警报，显示在“安全警报”仪表板中。 

![警报](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

请注意，在规则创建过程中确立的参数（搜索查询、阈值等）会在此自定义规则的警报中提供。

## <a name="see-also"></a>另请参阅
本文档介绍了如何在 Azure 安全中心创建自定义警报规则。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。


