---
title: Azure PowerShell 脚本示例 - 从快照创建托管磁盘 | Azure
description: Azure PowerShell 脚本示例 - 从快照创建托管磁盘
services: virtual-machines-linux
documentationcenter: storage
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 06/05/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: f49538a0f764f61a0f0c297b9a9aa1e3d8fe0dd6
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004435"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>使用 PowerShell 从快照创建托管磁盘

此脚本从快照创建托管磁盘。 使用它从 OS 和数据磁盘的快照还原虚拟机。 从各自的快照创建 OS 和数据托管磁盘，然后通过附加托管磁盘创建新的虚拟机。 还可以通过附加从快照创建的数据磁盘还原现有 VM 的数据磁盘。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!--[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]-->

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>示例脚本

```powershell
#Provide the subscription Id
$subscriptionId = 'yourSubscriptionId'

#Provide the name of your resource group
$resourceGroupName ='yourResourceGroupName'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'yourSnapshotName'

#Provide the name of the Managed Disk
$diskName = 'yourManagedDiskName'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'PremiumLRS'

#Provide the Azure region (e.g. chinanorth) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'chinanorth'

#Set the context to the subscription Id where Managed Disk will be created
Select-AzSubscription -SubscriptionId $SubscriptionId

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令从快照创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | 获取快照属性。  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 创建用于磁盘创建的磁盘配置。 包括父快照的资源 ID、与父快照位置相同的位置以及存储类型。  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 使用磁盘配置、磁盘名称和作为参数传递的资源组名称创建磁盘。 |

## <a name="next-steps"></a>后续步骤

[从托管磁盘创建虚拟机](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure Linux VM 文档](../linux/powershell-samples.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。

<!-- Update_Description: update meta properties, udpate powershell az cmdlet -->
<!-- ms.date: 04/01/2018 -->