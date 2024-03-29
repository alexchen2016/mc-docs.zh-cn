---
title: 在 Azure 中重新部署 Windows 虚拟机 | Azure
description: 如何通过在 Azure 中重新部署 Windows 虚拟机来缓解 RDP 连接问题。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 10/31/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: ef5f9c30f8f98a2092687fd75a7d39c155a8142b
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56665837"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>将 Windows 虚拟机重新部署到新的 Azure 节点
如果在对远程桌面 (RDP) 连接或应用程序对基于 Windows 的 Azure 虚拟机 (VM) 的访问进行故障排除时遇到困难，重新部署 VM 可能会有帮助。 重新部署 VM 时，Azure 会关闭该 VM，并将其移到 Azure 基础结构中的新节点，然后重新打开它，同时保留所有配置选项和关联的资源。 本文介绍了如何使用 Azure PowerShell 或 Azure 门户重新部署 VM。

> [!NOTE]
> 重新部署 VM 后，临时磁盘将丢失，与虚拟网络接口关联的动态 IP 地址将更新。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
请确保已在计算机上安装最新的 Azure PowerShell 1.x。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

以下示例部署名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM：

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>后续步骤
如果在连接 VM 时遇到问题，可以在 [troubleshooting RDP connections](troubleshoot-rdp-connection.md)（RDP 连接故障排除）或 [detailed RDP troubleshooting steps](detailed-troubleshoot-rdp.md)（详细的 RDP 故障排除步骤）中找到具体的帮助信息。 如果无法访问在 VM 上运行的应用程序，还可以阅读[应用程序故障排除问题](../windows/troubleshoot-app-connection.md)。

<!-- Update_Description: update meta properties -->