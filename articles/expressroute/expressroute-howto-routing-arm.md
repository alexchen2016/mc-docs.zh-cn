---
title: 配置 ExpressRoute 线路的对等互连：PowerShell：Azure
description: 本文指导完成创建和预配 ExpressRoute 线路的专用、公共和 Microsoft 对等互连的步骤。 本文还介绍如何检查状态，以及如何更新或删除线路的对等互连。
documentationcenter: na
services: expressroute
author: osamazia
manager: jonor
editor: ''
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/26/2019
ms.author: v-yiso
ms.date: 04/08/2019
ms.openlocfilehash: 5edddd56f99b2c3fc3ad927510b014bbc23f7f75
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626812"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>使用 PowerShell 创建和修改 ExpressRoute 线路的对等互连

本文帮助你使用 PowerShell 在资源管理器部署模型中创建和管理 ExpressRoute 线路的路由配置。 还可以检查 ExpressRoute 线路的状态，更新、删除和取消预配其对等互连。 如果想使用不同的方法处理线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> 
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)


这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。

> [!IMPORTANT]
> 我们目前无法通过服务管理门户播发服务提供商配置的对等互连。 我们正在努力不久就实现这一功能。 请在配置 BGP 对等互连之前与服务提供商核对。
> 
> 

可以为 ExpressRoute 线路配置一到三个对等互连（Azure 专用、Azure 公共和 Microsoft）。 可以按照所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。 有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 路由域](expressroute-circuit-peerings.md)。

## <a name="configuration-prerequisites"></a>配置先决条件

* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)页、[路由要求](expressroute-routing.md)页和[工作流](expressroute-workflows.md)页。
* 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md) ，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配和已启用状态，才能运行本文中的 cmdlet。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="private"></a>Azure 专用对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. 为 ExpressRoute 导入 PowerShell 模块。

   必须从 [PowerShell 库](https://www.powershellgallery.com/)安装最新的 PowerShell 安装程序，并将 Azure Resource Manager 模块导入 PowerShell 会话，以便开始使用 ExpressRoute cmdlet。 需要以管理员身份运行 PowerShell。

   ```powershell
   Install-Module Az
   ```

   导入已知语义版本范围内的所有 Az.\* 模块。

   ```powershell
   Import-Module Az
   ```

   也可以只导入已知语义版本范围内的 select 模块。

   ```powershell
   Import-Module Az.Network 
   ```

   登录到帐户。

   ```powershell
   Connect-AzAccount -EnvironmentName AzureChinaCloud
   ```

   选择要创建 ExpressRoute 线路的订阅。

   ```powershell
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-arm.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```powershell
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   其响应类似于如下示例：

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : chinanorth
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Beijing Telecom Ethernet",
                                       "PeeringLocation": "Beijing",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，请确保已准备好以下各项：

   * 主链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 辅助链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以将专用 AS 编号用于此对等互连。 请务必不要使用 65515。
   * 可选：
     * MD5 哈希（如果选择使用）。

   使用以下示例为线路配置 Azure 专用对等互连：

   ```powershell
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```powershell
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。
   > 
   >

### <a name="getprivate"></a>获取 Azure 专用对等互连详细信息

可以使用以下示例来获取配置详细信息：

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>更新 Azure 专用对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 100 更新为 500。

```powershell
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>删除 Azure 专用对等互连

可以运行以下示例来删除对等互连配置：

> [!WARNING]
> 运行此示例前，必须确保已删除所有虚拟网络。 
> 
> 

```powershell
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Azure 公共对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 公共对等互连配置。

### <a name="to-create-azure-public-peering"></a>创建 Azure 公共对等互连

1. 为 ExpressRoute 导入 PowerShell 模块。

   必须从 [PowerShell 库](https://www.powershellgallery.com/)安装最新的 PowerShell 安装程序，并将 Azure Resource Manager 模块导入 PowerShell 会话，以便开始使用 ExpressRoute cmdlet。 需要以管理员身份运行 PowerShell。

   ```powershell
   Install-Module Az
   ```

   导入已知语义版本范围内的所有 Az.\* 模块。

   ```powershell
   Import-Module Az
   ```

   也可以只导入已知语义版本范围内的 select 模块。

   ```powershell
   Import-Module Az.Network
   ```

   登录到帐户。

   ```powershell
   Connect-AzAccount
   ```

   选择要创建 ExpressRoute 线路的订阅。

   ```powershell
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-arm.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 公共对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```powershell
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   其响应类似于如下示例：

    ```
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : chinanorth
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Beijing Telecom Ethernet",
                                         "PeeringLocation": "Beijing",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []   
    ```

4. 配置线路的 Azure 公共对等互连。

   在继续下一步之前，请确保已准备以下信息。

   * 主链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 可选：
     * MD5 哈希（如果选择使用）。

   运行以下示例为线路配置 Azure 公共对等互连

   ```powershell
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```powershell
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。
   > 
   >

### <a name="getpublic"></a>获取 Azure 公共对等互连详细信息

可以使用以下 cmdlet 来获取配置详细信息：

```powershell
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>更新 Azure 公共对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 200 更新为 600。

```powershell
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>删除 Azure 公共对等互连

可以运行以下示例来删除对等互连配置：

```powershell
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


## <a name="next-steps"></a>后续步骤

下一步，[将 VNet 链接到 ExpressRoute 线路](./expressroute-howto-linkvnet-arm.md)。

-  有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](./expressroute-workflows.md)。

-  有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](./expressroute-circuit-peerings.md)。

-  有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。