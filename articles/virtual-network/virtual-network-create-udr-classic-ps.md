---
title: 控制 Azure 虚拟网络中的路由 - PowerShell - 经典 | Azure
description: 了解如何使用 PowerShell 控制 VNet 中的路由 | 经典
services: virtual-network
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/02/2016
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: 3207b1f9e606cf544d86721977bd11e4811e3bae
ms.sourcegitcommit: db9c7f1a7bc94d2d280d2f43d107dc67e5f6fa4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193069"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>使用 PowerShell 控制路由和使用虚拟设备（经典）

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell（经典）](virtual-network-create-udr-classic-ps.md)
> * [CLI（经典）](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> 在使用 Azure 资源之前，请务必了解 Azure 当前具有的两种部署模型：Azure 资源管理器和经典。 在使用任何 Azure 资源之前，请确保了解 [部署模型和工具](../azure-resource-manager/resource-manager-deployment-model.md) 。 可选择本文顶部的选项，查看不同工具的文档。 本文介绍经典部署模型。
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下述示例 Azure PowerShell 命令需要一个已经基于上述方案创建的简单环境。 如果想要运行本文档中所显示的命令，首先需要构建[使用 PowerShell 创建 VNet（经典）](virtual-networks-create-vnet-classic-netcfg-ps.md)中所述的环境。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>为前端子网创建 UDR
若要根据上述方案为前端子网创建所需的路由表和路由，请按照下面的步骤操作。

1. 运行以下命令，为前端子网创建路由表：

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location chinanorth `
    -Label "Route table for front end subnet"
    ```

2. 运行以下命令，在路由表中创建路由，将目标为后端子网 (192.168.2.0/24) 的所有流量发送到 **FW1** VM (192.168.0.4)：

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 运行以下命令，将路由表与 **FrontEnd** 子网关联：

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>为后端子网创建 UDR
若要根据方案为后端子网创建所需的路由表和路由，请完成以下步骤：

1. 运行以下命令为后端子网创建路由表：

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location chinanorth `
    -Label "Route table for back end subnet"
    ```

2. 运行以下命令，在路由表中创建路由，将目标为前端子网 (192.168.1.0/24) 的所有流量发送到 **FW1** VM (192.168.0.4)：

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 运行以下命令将路由表与 **BackEnd** 子网关联：

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>在 FW1 VM 上启用 IP 转发

若要在 FW1 VM 中启用 IP 转发，请完成以下步骤：

1. 运行以下命令，检查 IP 转发的状态：

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. 运行以下命令，为 *FW1* VM 启用 IP 转发：

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
<!-- Update_Description: update meta properties -->