---
title: 管理 Azure 安全中心的安全建议 | Azure Docs
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2019
ms.author: v-lingwu
ms.openlocfilehash: 4a3d48a4e9d4317b556cb39e367e9618bdf43478
ms.sourcegitcommit: 42766e267c2016d12977c24be394e8496f08e8fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66250290"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>管理 Azure 安全中心的安全建议
本文档介绍如何使用 Azure 安全中心中的建议帮助你保护 Azure 资源。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？
安全中心定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会创建建议。 此建议指导完成配置所需控件的过程。

## <a name="implementing-security-recommendations"></a>实施安全性建议
### <a name="set-recommendations"></a>设置建议
在[设置 Azure 安全中心的安全策略](tutorial-security-policy.md)中，了解到：

* 配置安全策略。
* 启用数据收集。
* 选择视作安全策略的一部分的建议。

当前的策略建议以系统更新、基线规则、反恶意程序、子网和网络接口的[网络安全组](../virtual-network/security-overview.md)、SQL 数据库审核、SQL 数据库透明数据加密和 Web 应用程序防火墙为中心。  [设置安全策略](tutorial-security-policy.md)提供每个建议选项的说明。

### <a name="monitor-recommendations"></a>监视建议
设置安全策略之后，安全中心将分析资源的安全状态，以识别潜在的漏洞。 通过“概述”  下的“建议”  磁贴，可以知道安全中心定义的建议总数。

![“建议”磁贴][1]

若要查看每项建议的详细信息，请选择“概述”  下的“建议”  磁贴。 这将打开“建议”  。

![筛选器建议][2]

可筛选建议。 要筛选建议，请选择“建议”边栏选项卡上的“筛选器”。   此时会打开“筛选器”  边栏选项卡，选择要查看严重性和状态值。


* **建议**：建议。
* **安全功能分数影响**：
* **资源**：列出了此建议适用的资源。
* **状态栏**：描述该特定建议的严重性：
   * **高（红色）** ：重要资源（如应用程序、VM 或网络安全组）存在漏洞，需要提请注意。
   * **中等（橙色）** ：存在漏洞，需要采取非关键步骤或额外步骤来消除它或完成某个过程。
   * **低（蓝色）** ：存在需要解决的漏洞，但不需立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。） 
   * **正常（绿色）** ：
   * **不可用（灰色）** ：
 


> [!NOTE]
> 想要了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)之间的差异。
> 
> 
> ### <a name="apply-recommendations"></a>应用建议
> 查看所有建议后，决定应首先应用哪个建议。 我们建议使用严重性等级作为主要参数来评估应首先应用的建议。



## <a name="next-steps"></a>后续步骤
在本文档中，已向你介绍安全中心的安全建议。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png


