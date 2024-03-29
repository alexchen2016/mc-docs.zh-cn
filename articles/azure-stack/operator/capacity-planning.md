---
title: Azure Stack 的容量规划 | Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/29/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 88aa316b8d24bdac78abed2e505c8ccdde5093f9
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381853"
---
# <a name="azure-stack-capacity-planning"></a>Azure Stack 容量规划
评估 Azure Stack 解决方案时，必须在硬件配置方面做出选择，因为它直接影响到 Azure Stack 云的总体容量。 这些是有关 CPU、内存密度、存储配置和总体解决方案规模或服务器数目的常规选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 这方面的第一个原因是 Azure Stack 构建为在解决方案自身内部托管基础结构或管理组件。 第二个原因是解决方案的某些容量保留用于支持复原；更新解决方案的软件时，必须将租户工作负荷的中断降到最低程度。

> [!IMPORTANT]
> 提供的容量规划信息和随附的电子表格只是为了指导你做出 Azure Stack 规划和配置方面的决策。 它们不是为了取代你自己的调查和分析。 

## <a name="compute-and-storage-capacity-planning"></a>计算和存储容量规划
Azure Stack 解决方案是作为超融合群集构建的，包含计算、网络和存储。 这样可以有效地使用或共享群集（称为 Azure Stack 的缩放单元）中的所有硬件容量，所采用的方式既提供可用性，又提供可伸缩性。 所有基础结构软件托管在一组虚拟机 (VM) 中，与租户 VM 共享相同的物理服务器。 然后所有 VM 可以通过缩放单元的 Windows Server 群集技术和独立的 Hyper-V 实例来管理。 此方法简化了 Azure Stack 解决方案的获取和管理，可以跨整个缩放单元移动和缩放所有服务（租户和体系结构）。

在 Azure Stack 解决方案中，唯一不会预配过度的物理资源是服务器内存。 其他资源（CPU 核心、网络带宽和存储容量）会预配过度，以便充分利用可用资源。 在计算解决方案的可用容量时，物理服务器内存是容量的主要依据。 然后，其他资源的利用率就是了解预配过度的比率的可能值与预期工作负荷可接受的值。

大约使用 30 个 VM 来托管 Azure Stack 的基础结构，总共使用约 230 GB 的内存和 140 个虚拟核心。 使用这个数目的 VM 是为了进行必要的服务隔离，使之符合安全性、可伸缩性、服务和修补方面的要求。 这种内部服务结构允许在将来引入新开发的基础结构服务。

为了自动更新所有物理服务器和基础结构软件组件，或者为了进行修补和更新，在进行基础结构和用户 VM 的放置时，不会使用缩放单元的所有内存资源。 在一个缩放单元的所有服务器的总内存中，一部分内存会取消分配，目的是满足解决方案的复原能力要求。 例如，在更新物理服务器的 Windows Server 映像时，会将托管在服务器上的 VM 移动到缩放单元中的别处，然后再更新服务器的 Windows Server 映像。 更新完成后，服务器会重启并继续处理工作负荷。 对 Azure Stack 解决方案进行修补和更新的目的是避免停止托管的 VM。 为了满足该目标，至少应取消分配一个服务器的内存容量，以便在缩放单元中移动 VM。 这样的放置和移动适用于基础结构 VM 以及代表 Azure Stack 解决方案的用户或租户创建的 VM。 最终的实施结果是，保留的用于支持必要的 VM 移动的内存量可能大大超出单个服务器的容量，因为如果 VM 的内存要求总是变化，则难以完成放置操作。 此类内存使用的一种额外开销是 Windows Server 实例本身的开销。 每个服务器的基础操作系统实例会耗用操作系统及其虚拟页表的内存，以及 Hyper-V 在管理每个托管的 VM 时使用的内存。

VM 容量取决于可用内存。 虚拟核心与物理核心的比率例证了 VM 密度对可用 CPU 容量造成的影响，解决方案在构建时使用了较大数目的物理核心（选择了另一 CPU）的情况除外。 存储容量和存储缓存容量的情况也是这样。

上述 VM 密度示例仅用于说明。 在提供支持时，所做的计算更为复杂。 若要进一步了解容量规划选择以及由此带来的可用容量，请与 Azure 联系。

此部分的剩余内容介绍针对计算和存储的 Azure Stack 部署要求。 可以根据此信息对所需组件及其最低配置值有一个基本的了解。 此外还提供其他信息，介绍如何根据可用容量来配置解决方案，以及系统在租户容量和性能方面的潜在限制。

> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 若要了解如何向 Azure Stack 添加更多的公共 IP 地址，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。


## <a name="next-steps"></a>后续步骤
[计算容量规划](capacity-planning-compute.md)
