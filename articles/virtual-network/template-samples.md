---
title: 适用于虚拟网络的 Azure 资源管理器模板示例 | Azure
description: 了解可供用来部署 Azure 虚拟网络的各种 Azure 资源管理器模板。
services: virtual-network
documentationcenter: virtual-network
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
origin.date: 04/22/2019
ms.date: 06/10/2019
ms.author: v-yeche
ms.openlocfilehash: eb7b7b1a42280064ccdae5c73ea04350331b913c
ms.sourcegitcommit: df1b896faaa87af1d7b1f06f1c04d036d5259cc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66250483"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>适用于虚拟网络的 Azure 资源管理器模板示例

下表包含 Azure 资源管理器模板示例的链接。 可以使用 Azure [门户](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fvirtual-network%2ftoc.json)、Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fvirtual-network%2ftoc.json) 或 Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fvirtual-network%2ftoc.json) 来部署模板。 若要了解如何创建自己的模板，请参阅[创建你的第一个模板](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fvirtual-network%2ftoc.json)和[了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fvirtual-network%2ftoc.json)。

> [!NOTE]
> 必须修改从 GitHub 存储库“azure-quickstart-templates”下载或参考的模板，以适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“cloudapp.chinacloudapi.cn”）；必要时更改某些不受支持的位置、VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

| | |
|----|----|
|[创建包含两个子网的虚拟网络](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| 创建包含两个子网的虚拟网络。|
|[通过网络虚拟设备的路由流量](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| 创建包含三个子网的虚拟网络。 将虚拟机部署到每个子网。 创建一个路由表，使其包含通过第三个子网中的虚拟机将流量从一个子网定向到另一个子网的路由。 将该路由表关联到其中一个子网。|
|[为 Azure 存储创建虚拟网络服务终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|创建包含两个子网的新虚拟网络，并在每个子网中创建一个网络接口。 为其中一个子网启用 Azure 存储的服务终结点，并将一个新的存储帐户保护到该子网。|
|[连接两个虚拟网络](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| 创建两个虚拟网络并在其之间创建虚拟网络对等互连。|
|[创建具有多个 IP 地址的虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| 创建具有多个 IP 地址的 Windows 或 Linux VM|

<!--Not Available on |[Configure IPv4 + IPv6 dual stack virtual network](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)-->
<!-- Update_Description: update meta properties, wording update -->