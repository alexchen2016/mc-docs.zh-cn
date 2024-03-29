---
title: 将 Log Analytics 警报扩展（复制）到 Azure China Cloud 云中
description: 概述了将警报从 OMS 门户中的 Log Analytics 复制到 Azure 警报的过程，并详细探讨了常见客户问题。
author: lingliw
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/21/19
ms.author: v-lingwu
ms.subservice: alerts
ms.openlocfilehash: ddfe388f9bca223f833c69e234fde328fd2a83dc
ms.sourcegitcommit: 7e25a709734f03f46418ebda2c22e029e22d2c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56440764"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>将 Log Analytics 警报扩展到 Azure 警报

> [!NOTE]
> Azure 已完成本文中针对 Azure 公共版所述的过程。 但是，它仍适用于美国政府版。  

Azure Log Analytics 包含自身的警报功能，可以根据 Log Analytics 数据将相关情况主动通知给你，这种情况到最近才有所改变。 过去是在 Azure Operations Management Suite 门户中管理预警规则。 新的警报体验现在已在世纪互联 Azure 的各种服务中集成了警报。 该体验在 Azure 门户中通过 Azure Monitor 下的“警报”提供，支持的警报来自活动日志、指标以及 Log Analytics 和 Azure Application Insights 提供的日志。

从 **2019 年 2 月 1 日**起，使用 OMS 门户的 Azure China Cloud 云客户可以[自愿将其警报规则扩展到 Azure](alerts-extend-tool.md)。 从 **2019 年 3 月 1 日**开始，Azure 会系统地将 Azure China Cloud - OMS 门户中的所有现有警报规则自动扩展到 Azure，而不会出现任何停机，也不会中断你的监视。 **2019 年 3 月 1 日**或之后在 Azure China Cloud 云 OMS 门户中创建的任何新工作区将自动扩展到 Azure。

## <a name="benefits-of-extending-your-alerts"></a>对警报进行扩展的好处
在 Azure 门户中创建和管理警报有多种优势，例如：

- 在 Operations Management Suite 门户中，只能创建和查看 250 个警报，与之不同，在 Azure 警报中不存在此限制。
- 在 Azure 警报中，可管理、枚举和查看所有警报类型。 在以前，只能对 Log Analytics 警报这样做。
- 可以使用 [Azure Monitor 角色](../../azure-monitor/platform/roles-permissions-security.md)将用户的访问权限限制为仅监视和警报。
- 在 Azure 警报中，可以使用[操作组](../../azure-monitor/platform/action-groups.md)。 这样就可以为每个警报设置多个操作。 可以发送短信、发送语音呼叫、调用 Azure 自动化 Runbook、调用 Webhook、配置 IT 服务管理 (ITSM) 连接器。 

## <a name="process-of-extending-your-alerts"></a>对警报进行扩展的流程
将警报从 Log Analytics 移到 Azure 警报中这一过程不涉及以任何方式更改警报定义、查询或配置。 唯一需要进行的更改是，在 Azure 中，你使用操作组执行所有操作。 如果操作组已与警报相关联，则在扩展到 Azure 中时，操作组也会包括进去。

在计划将 Log Analytics 工作区中的警报扩展到 Azure 中时，这些警报可以一直使用，不会以任何方式影响你的配置。 在计划以后，警报可能暂时不可修改；但在此期间，可以继续创建新的 Azure 警报。 如果尝试在 Operations Management Suite 门户中编辑或创建警报，可以选择在 Log Analytics 工作区中继续创建它们。 也可选择在 Azure 门户的 Azure 警报中创建它们。

 ![在 Log Analytics 或 Azure 警报中创建警报时的选项的屏幕截图](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> 将警报从 Log Analytics 扩展到 Azure 不会对帐户收费。 当在 [Azure Monitor 定价策略](https://www.azure.cn/pricing/details/monitor/)中声明的限制和条件范围内使用时，将 Azure 警报用于基于查询的 Log Analytics 警报不会计费。  


### <a name="how-to-extend-your-alerts-voluntarily"></a>如何主动扩展警报
若要将警报扩展到 Azure 警报，可以使用 Operations Management Suite 门户中提供的向导，也可以通过 API 以编程方式这样做。 有关详细信息，请参阅[使用 Operations Management Suite 门户和 API 将警报扩展到 Azure](alerts-extend-tool.md)。

## <a name="experience-after-extending-your-alerts"></a>扩展警报后的体验
将警报扩展到 Azure 警报以后，可以继续在 Operations Management Suite 门户中对其进行管理，与之前无异。

![Operations Management Suite 门户的屏幕截图，其中列出了警报](media/alerts-extend/PostExtendList.png)

在 Operations Management Suite 门户中尝试编辑现有警报或创建新警报时，会自动重定向到 Azure 警报。  

> [!NOTE]
> 确保在 Azure 中正确分配需要添加或编辑警报的个人用户的权限。 若要了解需授予的具体权限，请参阅[使用 Azure Monitor 和警报的权限](../../azure-monitor/platform/roles-permissions-security.md)。  
> 

可以继续通过 [Log Analytics API](../../azure-monitor/platform/api-alerts.md) 和 [Log Analytics 资源模板](../../azure-monitor/insights/solutions-resources-searches-alerts.md)创建警报。 执行此操作时，必须包括操作组。

## <a name="next-steps"></a>后续步骤

* 了解[启动将警报从 Log Analytics 扩展到 Azure 的流程](alerts-extend-tool.md)时使用的工具。
* 详细了解 [Azure 警报体验](../../azure-monitor/platform/alerts-overview.md)。
* 了解然后创建 [Azure 警报中的日志警报](alerts-unified-log.md)。





