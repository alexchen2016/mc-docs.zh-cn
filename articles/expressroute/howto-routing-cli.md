---
title: '如何配置线路的对等互连 - ExpresssRoute：Azure CLI '
description: 本文介绍了如何创建和预配 ExpressRoute 线路的专用、公共和 Microsoft 对等互连。 本文还介绍如何检查状态，以及如何更新或删除线路的对等互连。
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 10/23/2018
ms.author: v-yiso
ms.date: 04/08/2019
ms.openlocfilehash: 21e2f35d1d6322a9eba24f9085f25ecb0bb4fafd
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627572"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>使用 CLI 创建和修改 ExpressRoute 线路的对等互连

本文介绍如何使用 CLI 在资源管理器部署模型中创建和管理 ExpressRoute 线路的路由配置/对等互连。 还可以检查状态，以及更新、删除和取消预配 ExpressRoute 线路的对等互连。 如果想使用不同的方法处理线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>配置先决条件

* 在开始之前，请安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-lastest)。
* 在开始配置前，请务必查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)页面。
* 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](howto-circuit-cli.md) ，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配和已启用状态，这样你才能运行本文中的命令。

这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。

可以为 ExpressRoute 线路配置一个或两个对等互连（Azure 专用、Azure 公共）。 可以按照所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。  有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 路由域](expressroute-circuit-peerings.md)。

## <a name="private"></a>Azure 专用对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. 安装最新版本的 Azure CLI。 必须使用最新版本的 Azure 命令行接口 (CLI)。* 在开始配置前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

   ```azurecli
   az login
   ```

   选择要创建 ExpressRoute 线路的订阅

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。 请按说明创建 [ExpressRoute 线路](howto-circuit-cli.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   其响应类似于如下示例：

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "chinanorth",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Beijing",
   "serviceProviderName": "Beijing Telecom Ethernet"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，请确保已准备好以下各项：

   * 主链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 辅助链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以将专用 AS 编号用于此对等互连。 请务必不要使用 65515。
   * **可选** - MD5 哈希（如果选择使用）。

   使用以下示例为线路配置 Azure 专用对等互连：

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。
   > 
   > 

### <a name="getprivate"></a>查看 Azure 专用对等互连详细信息

可以使用以下示例来获取配置详细信息：

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

输出类似于以下示例：

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>更新 Azure 专用对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 100 更新为 500。

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>删除 Azure 专用对等互连

可以运行以下示例来删除对等互连配置：

> [!WARNING]
> 运行此示例前，必须确保已删除所有虚拟网络和 ExpressRoute Global Reach 连接。 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Azure 公共对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 公共对等互连配置。

### <a name="to-create-azure-public-peering"></a>创建 Azure 公共对等互连

1. 安装最新版本的 Azure CLI。 必须使用最新版本的 Azure 命令行接口 (CLI)。* 在开始配置前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

   ```azurecli
   az login
   ```

   选择要为其创建 ExpressRoute 线路的订阅。

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。  请按说明创建 [ExpressRoute 线路](howto-circuit-cli.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 公共对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```azurecli
   az network express-route list
   ```

   其响应类似于如下示例：

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "chinanorth",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Beijing",
    "serviceProviderName": "Beijing Telecom Ethernet"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. 配置线路的 Azure 公共对等互连。 在继续下一步之前，请确保已准备以下信息。

   * 主链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * **可选** - MD5 哈希（如果选择使用）。

   运行以下示例为线路配置 Azure 公共对等互连：

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。

### <a name="getpublic"></a>查看 Azure 公共对等互连详细信息

可以使用以下示例来获取配置详细信息：

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

输出类似于以下示例：

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>更新 Azure 公共对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 200 更新为 600。

```azurecli
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>删除 Azure 公共对等互连

可以运行以下示例来删除对等互连配置：

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```


## <a name="next-steps"></a>后续步骤

下一步，[将 VNet 链接到 ExpressRoute 线路](howto-linkvnet-cli.md)。

* 有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。