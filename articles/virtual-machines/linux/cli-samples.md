---
title: Azure CLI 示例 | Azure
description: Azure CLI 示例
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 03/01/2019
ms.date: 05/20/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 68b6cb1d5401e6179459b28cc29b731f9b1b9543
ms.sourcegitcommit: 70289159901086306dd98e55661c1497b7e02ed9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276466"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>适用于 Linux 虚拟机的 Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建虚拟机**||
| [创建虚拟机](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 使用最小配置创建 Linux 虚拟机。 |
| [创建完全配置的虚拟机](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 创建资源组、虚拟机以及所有相关资源。|
| [创建高度可用的虚拟机](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 NGINX。 |
| [创建安装有 WordPress 的 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 WordPress。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 |
| [从快照创建 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 先从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 |
|**管理存储**||
| [从 VHD 创建托管磁盘](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 从作为 OS 磁盘的专用 VHD 或从作为数据磁盘的数据 VHD 创建托管磁盘。  |
| [从快照创建托管磁盘](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 从快照创建托管磁盘。 |
| [将托管磁盘复制到相同或不同的订阅](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 将托管磁盘复制到相同或不同的订阅，但与父级托管磁盘位于同一区域。 
| [将快照作为 VHD 导出到存储帐户](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 将托管快照作为 VHD 导出到不同区域中的存储帐户。 |
| [将托管磁盘的 VHD 导出到存储帐户](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 将托管磁盘的基础 VHD 导出到不同区域中的存储帐户。 |
| [将快照复制到相同或不同的订阅中](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 将快照复制到相同或不同订阅（但是与父快照处于相同区域中）。 |
|**网络虚拟机**||
| [保护虚拟机之间的网络流量](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 创建两个虚拟机、所有相关资源以及内部和外部网络安全组 (NSG)。 |
|**保护虚拟机**||
| [加密 VM 和数据磁盘](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 创建 Azure Key Vault、加密密钥和服务主体，然后对 VM 进行加密。 |
|**对虚拟机进行故障排除**||
| [对 VM 操作系统磁盘进行故障排除](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | 将一个 VM 中的操作系统磁盘作为数据磁盘装载到第二个 VM 中。 |
| | |

<!--Not Available on | [Monitor a VM with Azure Monitor logs](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fvirtual-machines%2flinux%2ftoc.json) | Creates a virtual machine, installs the Log Analytics agent, and enrolls the VM in an Log Analytics workspace.  |-->
<!--Pending on virtual-machines-linux-cli-sample-create-vm-oms.md-->

<!--Update_Description: update meta properties -->
