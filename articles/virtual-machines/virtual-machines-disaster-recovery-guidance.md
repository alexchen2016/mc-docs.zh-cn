---
title: Azure VM 灾难恢复方案 | Azure
description: 了解发生影响 Azure 虚拟机的 Azure 服务中断事件时该怎么办。
services: virtual-machines
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/31/2017
ms.date: 05/14/2018
ms.author: v-yeche
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb33c47bc80235380fdebf54e6960f1d203ec09f
ms.sourcegitcommit: 33421c72ac57a412a1717a5607498ef3d8a95edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2018
ms.locfileid: "53785158"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>发生影响 Azure VM 的 Azure 服务中断事件时该怎么办
Azure.cn 的同仁兢兢业业，只为确保在任何时候都能提供需要的服务。 但有时候会因为不可抗力的影响，造成服务意外中断。

Azure.cn 为其服务提供服务级别协议 (SLA)，作为运行时间和连接承诺。 可以在 [Azure 服务级别协议](https://www.azure.cn/support/legal/sla/)中找到各种 Azure 服务的 SLA。

Azure 已在平台中内置多种功能，用于支持高度可用的应用程序。

本文介绍了当整个区域因重大自然灾难或大规模服务中断而发生中断时的真实灾难恢复方案。 这些都是极其罕见的情况，但你还是必须对整个区域发生中断的可能性有所准备。 如果整个区域的服务中断，会暂时无法使用数据的本地冗余副本。 如果启用了异地复制，则会在其他区域额外存储 Azure 存储 blob 和表的三个副本。 如果发生全面性区域中断或发生主要区域无法恢复的灾难，Azure 会将所有 DNS 条目重新映射到异地复制区域。

为帮助你处理这些罕见事件，我们提供以下 Azure 虚拟机指导，以应对 Azure 虚拟机应用程序部署所在的整个区域发生服务中断的情况。

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>选项 1：使用 Azure Site Recovery 启动故障转移
可以为 VM 配置 Azure Site Recovery，只需单击一下，几分钟内即可恢复应用程序。 可以将虚拟机复制到所选的 Azure 区域，而不局限于配对区域。 可以通过[复制虚拟机](https://aka.ms/a2a-getting-started)来启动。 可以[创建恢复计划](../site-recovery/site-recovery-create-recovery-plans.md)，以便可以对应用程序自动完成整个故障转移过程。 可以预先[测试故障转移](../site-recovery/site-recovery-test-failover-to-azure.md)，这不会影响生成应用程序或正在进行的复制。 在主要区域中断的情况下，只需[启动故障转移](../site-recovery/site-recovery-failover.md)并在将应用程序移至目标区域。

## <a name="option-2-wait-for-recovery"></a>选项 2：等待恢复
在此情况下，不需要采取任何操作。 但要知道，我们正在努力还原服务的可用性。 可在 [Azure 服务运行状况仪表板](https://www.azure.cn/support/service-dashboard/)上查看当前服务状态。

如果发生中断前尚未设置 Azure Site Recovery、读取访问权限异地冗余存储或异地冗余存储，这便是你的最佳选项。 如果存储 VM 虚拟硬盘 (VHD) 的存储帐户已设置异地冗余存储或读取访问权限异地冗余存储，可以指望恢复基本映像 VHD，并尝试用它预配新的 VM。 由于不能保证同步数据，所以这不是首选的选项。 因此，不保证此选项可行。

> [!NOTE]
> 注意，对此过程无任何控制权，并且此过程仅适用于区域范围的服务中断。 因此，还必须依靠应用程序特有的其他备份方法才能达到最高级别的可用性。 有关详细信息，请参阅 [Data strategies for disaster recovery](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery)（灾难恢复的数据策略）部分。
>
>

## <a name="next-steps"></a>后续步骤

- 使用 Azure Site Recovery [保护在 Azure 虚拟机上运行的应用程序](https://aka.ms/a2a-getting-started)

- 若要详细了解如何实现灾难恢复和高可用性策略，请参阅 [Azure 应用程序的灾难恢复和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

- 若要掌握有关云平台功能的详细技术知识，请参阅 [Azure 复原技术指南](../resiliency/resiliency-technical-guidance.md)。

- 如果指示不清楚，或者如果希望 Azure.cn 代你执行操作，请联系[客户支持](https://support.azure.cn/zh-cn/support/support-azure/)。
<!-- Update_Description: update meta properties -->