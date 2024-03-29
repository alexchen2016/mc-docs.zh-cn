---
title: Azure Analysis Services 高可用性 | Azure
description: 确保 Azure Analysis Services 高可用性。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: c2051a27bdda821b662d0911c8f8e9b5ba928f97
ms.sourcegitcommit: b24f0712fbf21eadf515481f0fa219bbba08bd0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55085651"
---
# <a name="analysis-services-high-availability"></a>Analysis Services 高可用性

本文说明如何确保 Azure Analysis Services 服务器的高可用性。 

## <a name="assuring-high-availability-during-a-service-disruption"></a>在服务中断过程中确保高可用性

虽然罕见，但 Azure 数据中心可能会发生服务中断。 发生服务中断时，可能会导致业务中断持续几分钟，也可能持续数小时。 通常，通过服务器冗余实现高可用性。 借助 Azure Analysis Services，可以通过在一个或多个区域中创建附加的辅助服务器来实现冗余。 创建冗余服务器时，若要确保这些服务器上的数据和元数据与区域中已脱机的服务器同步，可以执行以下操作：

* 将模型部署到其他区域中的冗余服务器。 此方法要求在主服务器和冗余服务器中并行处理数据，以确保所有服务器同步。

* 从主服务器[备份](analysis-services-backup.md)数据库，并在冗余服务器上还原。 例如，可以每夜自动备份到 Azure 存储，并还原到其他区域中的其他冗余服务器。 

在上述任一情况下，如果主服务器发生服务中断，都必须更改报表客户端中的连接字符串，以连接到不同区域数据中心中的服务器。 此更改应视为最后手段，仅在发生灾难性区域数据中心服务中断时适用。 很可能在更新所有客户端上的连接之前，托管主服务器的数据中心服务中断会恢复到联机状态。 

为避免必须更改报告客户端上的连接字符串，可以为主服务器创建一个服务器[别名](analysis-services-server-alias.md)。 如果主服务器出现故障，你可以更改别名以指向另一个区域中的冗余服务器。 可以通过在主服务器上编写终结点运行状况检查代码来自动更改指向服务器名称的别名。 如果运行状况检查失败，则同一终结点可以定向到另一个区域中的冗余服务器。 

## <a name="related-information"></a>相关信息

[备份和还原](analysis-services-backup.md)   
[管理 Azure Analysis Services](analysis-services-manage.md)   
[别名服务器名称](analysis-services-server-alias.md)

<!--Update_Description: update meta properties -->
