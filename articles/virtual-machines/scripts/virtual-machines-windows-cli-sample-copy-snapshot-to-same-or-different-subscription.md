---
title: Azure CLI 脚本示例 - 使用 CLI 将托管磁盘的快照复制（移动）到相同或不同的订阅 | Azure
description: Azure CLI 脚本示例 - 使用 CLI 将托管磁盘的快照复制（移动）到相同或不同的订阅
services: virtual-machines-windows
documentationcenter: storage
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 05/19/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 7611e6090834918f01e684365bfa71789a571980
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004339"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>使用 CLI 将托管磁盘的快照复制到相同或不同的订阅

此脚本会将托管磁盘的快照复制到相同或不同的订阅。 将此脚本用于以下方案：

1. 将高级存储 (Premium_LRS) 中的快照迁移到标准存储（Standard_LRS 或 Standard_ZRS）以降低成本。
1. 将快照从本地冗余存储（Premium_LRS、Standard_LRS）迁移到区域冗余存储（Standard_ZRS），以从 ZRS 存储的更高可靠性中受益。
1. 将快照移到同一区域中的不同订阅，以延长保留时间。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#Provide the subscription Id of the subscription where snapshot exists
sourceSubscriptionId=dd80b94e-0463-4a65-8d04-c94f403879dc

#Provide the name of your resource group where snapshot exists
sourceResourceGroupName=mySourceResourceGroupName

#Provide the name of the snapshot
snapshotName=mySnapshotName

#Set the context to the subscription Id where snapshot exists
az account set --subscription $sourceSubscriptionId

#Get the snapshot Id 
snapshotId=$(az snapshot show --name $snapshotName --resource-group $sourceResourceGroupName --query [id] -o tsv)

#If snapshotId is blank then it means that snapshot does not exist.
echo 'source snapshot Id is: ' $snapshotId

#Provide the subscription Id of the subscription where snapshot will be copied to
#If snapshot is copied to the same subscription then you can skip this step
targetSubscriptionId=6492b1f7-f219-446b-b509-314e17e1efb0

#Name of the resource group where snapshot will be copied to
targetResourceGroupName=mytargetResourceGroupName

#Set the context to the subscription Id where snapshot will be copied to
#If snapshot is copied to the same subscription then you can skip this step
az account set --subscription $targetSubscriptionId

#Copy snapshot to different subscription using the snapshot Id
#We recommend you to store your snapshots in Standard storage to reduce cost. Please use Standard_ZRS in regions where zone redundant storage (ZRS) is available, otherwise use Standard_LRS
#Please check out the availability of ZRS here: https://docs.azure.cn/storage/common/storage-redundancy-zrs#support-coverage-and-regional-availability
az snapshot create --resource-group $targetResourceGroupName --name $snapshotName --source $snapshotId --sku Standard_LRS

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令，通过源快照的 ID 在目标订阅中创建快照。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az snapshot show](https://docs.azure.cn/zh-cn/cli/snapshot?view=azure-cli-latest#az-snapshot-show) | 使用快照的名称和资源组属性获取该快照的所有属性。 使用 ID 属性将快照复制到其他订阅。  |
| [az snapshot create](https://docs.azure.cn/zh-cn/cli/snapshot?view=azure-cli-latest#az-snapshot-create) | 通过使用父快照的 ID 和名称在不同订阅中创建快照来复制快照。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/index?view=azure-cli-latest)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。

<!-- Update_Description: update meta properties, udpate powershell az cmdlet -->
<!-- ms.date: 04/01/2018 -->