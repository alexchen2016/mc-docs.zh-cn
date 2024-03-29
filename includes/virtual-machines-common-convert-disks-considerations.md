---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 7a69504487cacadd047211973a03f208cc5c89f8
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52676340"
---
* 该转换需要重启 VM，因此请在预先存在的维护时段内计划 VM 迁移。 

* 该转换是不可逆的。 

* 请注意，任何具有[虚拟机参与者](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色的用户将不能更改 VM 大小（因为它们可以预转换）。 这是因为包含托管磁盘的 VM 要求用户对 OS 磁盘具有 Microsoft.Compute/disks/write 权限。

* 请务必测试转换。 在生产环境中执行迁移之前迁移测试性虚拟机。

* 在转换期间，会解除分配 VM。 转换完成后，VM 在启动时会接收新的 IP 地址。 如果需要，可向 VM [分配静态 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)。

* 不会删除在转换之前由 VM 使用的原始 VHD 和存储帐户。 它们会继续产生费用。 若要避免这些项目产生的费用，请在验证转换已完成后删除原始 VHD Blob。

* 查看支持转换过程所需的 Azure VM 代理最低版本。 有关如何检查和更新目标版本的信息，请参阅 [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)（对 Azure 中的 VM 代理的最低版本支持）

<!-- Update_Description: update meta properties  -->