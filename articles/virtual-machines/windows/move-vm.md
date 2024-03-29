---
title: 在 Azure 中移动 Windows VM 资源 | Azure
description: 在 Resource Manager 部署模型中将 Windows VM 移到其他 Azure 订阅或资源组。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 09/12/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 1fe8b44d9d092b5fec705ebd579e7086ea13a641
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56666065"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>将 Windows VM 移到其他 Azure 订阅或资源组
本文逐步说明如何在资源组或订阅之间移动 Windows 虚拟机 (VM)。 如果最初在个人订阅中创建了 VM，现在想要将其移到公司的订阅以继续工作，则在订阅之间移动 VM 可能很方便。

> [!IMPORTANT]
>在移动过程中会创建新的资源 ID。 移动 VM 后，需要更新工具和脚本以使用新的资源 ID。 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>使用 PowerShell 移动 VM

要将虚拟机移到其他资源组，需确保同时移动所有依赖资源。 要获取每种资源的资源 ID 列表，请使用 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet。

```powershell
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

可以使用上一命令的输出作为资源 ID 的逗号分隔列表，以用于 [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource)，将每种资源移到目标。 

```powershell
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

要将资源移到其他订阅，请包含 **-DestinationSubscriptionId** 参数的值。 

```powershell
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

当系统要求你确认是否要移动指定的资源时，请输入 **Y** 进行确认。

## <a name="next-steps"></a>后续步骤
可以在资源组和订阅之间移动许多不同类型的资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../resource-group-move-resources.md)。

<!-- Update_Description: update meta properties, wording update -->