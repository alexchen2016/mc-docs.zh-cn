---
title: '使用 PowerShell 为 ExpressRoute 配置 VNet 网关（经典）：Azure '
description: 在 ExpressRoute 配置中使用 PowerShell 配置经典部署模型 VNet 的 VNet 网关。
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 11/05/2018
ms.date: 12/10/2018
ms.author: v-yiso
ms.openlocfilehash: 4f06a7b162f6e269c606043a64fc7bd5773d755c
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674223"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>使用 PowerShell 配置 ExpressRoute 的虚拟网络网关（经典）
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](./expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](./expressroute-howto-add-gateway-classic.md)
>
>

本文指导你完成为预先存在的 VNet 添加、重设大小和删除虚拟网络 (VNet) 网关的步骤。 此配置的步骤专用于使用**经典部署模型**创建的、会在 ExpressRoute 配置中使用的 VNet。 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>开始之前

确认已安装此配置所需的 Azure PowerShell cmdlet（1.0.2 或更高版本）。 如果尚未安装 cmdlet，必须先安装，才能开始执行配置步骤。 有关安装 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>后续步骤

创建 VNet 网关之后，可以将 VNet 链接到 ExpressRoute 线路。 请参阅[将虚拟网络链接到 ExpressRoute 线路](./expressroute-howto-linkvnet-classic.md)。