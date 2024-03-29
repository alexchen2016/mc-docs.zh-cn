---
title: Azure Monitor 品牌重塑 | Azure Docs
description: 介绍了最近对 Azure 管理服务所做的品牌和名称更改。
author: lingliw
manager: digimobile
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/19
ms.author: v-lingwu
ms.openlocfilehash: 6a1633416a38430204d1bb4f760e662dac2d8f22
ms.sourcegitcommit: 7e25a709734f03f46418ebda2c22e029e22d2c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56440447"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor 命名和术语更改
最近对 Azure Monitor 进行了重要更改，整合了不同的服务，目的是为 Azure 客户简化监视过程。 本文介绍最近在 Azure Monitor 文档中所做的名称和术语更改。

## <a name="february-2019---log-analytics-terminology"></a>2019 年 2 月 - Log Analytics 术语
将不同的服务合并到 Azure Monitor 下以后，我们将执行下一步，修改文档中的术语，以便更好地描述 Azure Monitor 服务及其不同的组件。 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor 日志数据仍然存储在 Log Analytics 工作区中，并仍然由同一 Log Analytics 服务收集并分析，但我们会在许多位置将术语 _Log Analytics_ 更改为“Azure Monitor 日志”。 该术语更好地反映其在 Azure Monitor 中的角色，提供更好的一致性[指标（在 Azure Monitor 中）](platform/data-collection.md)。

术语 _log analytics_ 现在主要应用于 Azure 门户中的页面，用于编写和运行查询以及分析日志数据。 它在功能上相当于[指标资源管理器](platform/metrics-charts.md)，后者是 Azure 门户中的页面，用于分析指标数据。

### <a name="log-analytics-workspaces"></a>Log Analytics 工作区
在 Azure Monitor 中保存日志数据的[工作区](platform/manage-access.md)仍然称为 Log Analytics 工作区。 Azure 门户中的“Log Analytics”页面已重命名为“Log Analytics 工作区”，是创建新工作区和配置数据源的地方。 在 **Azure Monitor** 中分析日志和其他监视数据，在 **Log Analytics 工作区**中配置工作区。

### <a name="management-solutions"></a>管理解决方案
[管理解决方案](insights/solutions.md)已重命名为“监视解决方案”，以便更好地描述其功能。


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018 年 8 月 - 将监视服务合并到了 Azure Monitor 中
Log Analytics 和 Application Insights 已整合到 Azure Monitor 中，以便为 Azure 资源和混合环境的监视提供单一集成的体验。 没有从这些服务中删除任何功能，并且用户可以与之前一样执行相同的方案，没有失去或减弱任何功能。

这些服务的文档已整合到 Azure Monitor 的单个文档集中。 这有助于读者在单个位置中查找适用于特定监视方案的所有内容，不必参考多个内容集。 随着整合的服务的发展，内容将变得更加集成。

之前被视为 Log Analytics 一部分的其他功能（例如代理和视图）也已重新定位为 Azure Monitor 的功能。 它们的功能没有更改，只是可能改进了它们在 Azure 门户中的体验。


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018 年 4 月 - 停用了 Operations Management Suite 品牌
Operations Management Suite (OMS) 之前捆绑到以下 Azure 管理服务，用于进行许可授权：

- Application Insights
- Azure 自动化
- Azure 备份
- Log Analytics
- 站点恢复

[已经为这些服务引入了新的定价](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)，并且 OMS 捆绑不再可供新客户使用。 之前属于 OMS 的一部分的服务未发生更改，只是如上所述整合到了 Azure Monitor 中。 




## <a name="next-steps"></a>后续步骤

- 阅读 [Azure Monitor 概述](overview.md)，其中介绍了它的各种组件和功能。



