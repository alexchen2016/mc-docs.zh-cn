---
title: 使用 Azure PowerShell 管理虚拟机 | Azure
description: 了解可用于在虚拟机管理过程中自动执行任务的命令。
services: virtual-machines-windows
documentationcenter: windows
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
origin.date: 10/12/2016
ms.date: 05/21/2018
ms.author: v-yeche
ms.openlocfilehash: d85e7ff1e465e74ae3189ad92576657313fc040e
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625855"
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>使用 Azure PowerShell 管理虚拟机
> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 有关使用 Resource Manager 模型的常见 PowerShell 命令，请参阅[此处](../../virtual-machines-windows-ps-common-ref.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。

每天执行的许多管理 VM 的任务都可通过使用 Azure PowerShell cmdlet 自动执行。 本文以适用于较简单任务的命令为例加以说明，并提供演示适用于更复杂任务的命令的文章链接。

> [!NOTE]
> 如果尚未安装和配置 Azure PowerShell，可在[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 一文中获取相关说明。
> 
> 

## <a name="how-to-use-the-example-commands"></a>如何使用示例命令
需要将命令中的一些文本替换为适合环境的文本。 < 和 > 符号指示需要替换的文本。 替换文本时，请删除符号，但将引号保留在原处。

## <a name="get-a-vm"></a>获取 VM
这是经常会使用的基本任务。 使用它可获取有关 VM 的信息、对 VM 执行任务或获取输出以存储在变量中。

若要获取有关 VM 的信息，请运行此命令，并替换引号内的所有内容（包括 < 和 > 字符）：

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

要将输出存储在 $vm 变量中，请运行：

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>登录到基于 Windows 的 VM
运行以下命令：

> [!NOTE]
> 可以从显示的 **Get-AzureVM** 命令中获取虚拟机和云服务名称。
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<用于存储下载的 RDP 文件的驱动器和文件夹位置，示例：c:\temp>" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>停止 VM
运行以下命令：

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> 如果该 VM 是云服务中的最后一个 VM，则使用此参数可以保留该云服务的虚拟 IP (VIP)。 <br><br> 如果使用 StayProvisioned 参数，则仍要支付 VM 的费用。
> 
> 

## <a name="start-a-vm"></a>启动 VM
运行以下命令：

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>附加数据磁盘
此任务需要几个步骤才能完成。 首先，使用 ***<em>Add-AzureDataDisk</em>*** cmdlet 将磁盘添加到 $vm 对象。 然后，使用 **Update-AzureVM** cmdlet 更新 VM 的配置。

还需要确定是要附加新的磁盘还是附加包含数据的磁盘。 对于新磁盘，此命令创建并附加 .vhd 文件。

若要附加新磁盘，请运行以下命令：

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

若要附加现有数据磁盘，请运行以下命令：

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

若要从 blob 存储中现有的 .vhd 文件附加数据磁盘，请运行以下命令：

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.chinacloudapi.cn/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>创建基于 Windows 的 VM
若要在 Azure 中创建新的基于 Windows 的虚拟机，请使用[使用 Azure PowerShell 创建和预配置基于 Windows 的虚拟机](create-powershell.md)中的说明。 本主题介绍如何创建 Azure PowerShell 命令集，以创建可预先配置的基于 Windows 的 VM：

* 使用 Active Directory 域成员身份。
* 使用附加磁盘。
* 作为现有负载均衡集的成员。
* 使用静态 IP 地址。

<!-- Update_Description: update meta properties -->