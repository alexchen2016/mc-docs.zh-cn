---
title: 连接云服务中的 Windows VM | Azure
description: 将使用经典部署模型创建的 Windows 虚拟机连接到 Azure 云服务或虚拟网络。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 06/06/2017
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 9099a80b6c162ea6a635c178f88fb8b9a4c4d688
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675610"
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>将使用经典部署模型创建的 Windows 虚拟机连接到虚拟网络或云服务
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。

使用经典部署模型创建的 Windows 虚拟机始终放置在云服务中。 云服务充当容器，并提供唯一的公用 DNS 名称、公用 IP 地址，以及一组通过 Internet 访问虚拟机的终结点。 云服务可以位于虚拟网络中，但这不是必要条件。

如果云服务不在虚拟网络中，就称为 *独立* 云服务。 独立云服务中的虚拟机使用其他虚拟机的公共 DNS 名称与其通信，流量通过 Internet 传送。 如果云服务是在虚拟网络中，则该云服务中的虚拟机可与虚拟网络中的其他所有虚拟机通信，而不需要通过 Internet 传送任何流量。

如果将虚拟机放在相同的独立云服务中，仍然可以使用负载均衡和可用性集。 有关详细信息，请参阅[对虚拟机进行负载均衡](../../virtual-machines-windows-load-balance.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)和[管理虚拟机的可用性](../../virtual-machines-windows-manage-availability.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。 不过，无法组织子网上的虚拟机，也无法将独立云服务连接到本地网络。 下面是一个示例：

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>后续步骤
创建虚拟机后，建议[添加数据磁盘](attach-disk.md)，以便服务和工作负荷有地方存储数据。

<!-- Update_Description: update meta properties -->
