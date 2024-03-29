---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 05/17/2018
ms.date: 01/14/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 1260c74b227cebe5adf05ef835553f259cb18645
ms.sourcegitcommit: 4f91d9bc4c607cf254479a6e5c726849caa95ad8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53996465"
---
### <a name="virtual-networks"></a>虚拟网络

|  |  |
|---------|---------|
| [允许的应用程序网关 SKU](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | 要求应用程序网关使用已批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [允许的 vNet 网关 SKU](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | 要求虚拟网关使用已批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [允许的负载均衡器 SKU](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | 要求虚拟网络负载均衡器使用已批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [没有与 Express Route 网络的网络对等互连](../articles/governance/policy/samples/no-peering-express-route-network.md) | 禁止将网络对等关联到指定资源组中的网络。 用于防止与中心托管的网络基础结构连接。 指定要防止关联的资源组的名称。 |
| [无用户定义的路由表](../articles/governance/policy/samples/no-user-defined-route-table.md)  | 禁止通过用户定义的路由表部署虚拟网络。 |
| [每个子网上的 NSG X](../articles/governance/policy/samples/nsg-on-subnet.md) | 要求将特定网络安全组用于所有子网。 指定要使用的网络安全组的 ID。 |
| [对 VM 网络接口使用已批准的子网](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | 要求网络接口使用已批准的子网。 指定已批准的子网的 ID。 |
| [对 VM 网络接口使用已批准的 vNet](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | 要求网络接口使用已批准的虚拟网络。 由你指定已批准的虚拟网络的 ID。 |