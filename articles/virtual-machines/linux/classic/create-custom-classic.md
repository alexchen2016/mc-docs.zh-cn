---
title: 使用 Azure 经典 CLI 创建经典 Linux VM | Azure
description: 了解如何通过 Azure 经典 CLI 使用经典部署模型创建 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
origin.date: 02/09/2017
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: fef8c8864922344fd14a425f5eafc3ec3dcbaabc
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674998"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>如何使用 Azure 经典 CLI 创建经典 Linux VM
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 有关 Resource Manager 版本，请参阅[此处](../create-cli-complete.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。

本主题介绍如何通过 Azure 经典 CLI 使用经典部署模型创建 Linux 虚拟机 (VM)。 我们使用 Azure 上可用**映像**中的 Linux 映像。 Azure 经典 CLI 命令提供以下配置选项以及其他配置选项：

* 将 VM 连接到虚拟网络
* 将 VM 添加到现有云服务
* 将 VM 添加到现有存储帐户
* 将 VM 添加到可用性集或位置

> [!IMPORTANT]
> 如果希望 VM 使用虚拟网络，以便可以按主机名直接连接到它或者设置跨界连接，请确保在创建 VM 时指定虚拟网络。 仅在创建 VM 时，才能将该 VM 配置为加入虚拟网络。 有关虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](/virtual-network/virtual-networks-overview)。
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>如何使用经典部署模型创建 Linux VM
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

<!-- Update_Description: update meta properties, wording update -->
