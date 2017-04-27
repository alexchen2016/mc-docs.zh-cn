---
title: "使用 PowerShell 将数据磁盘附加到 Azure 中的 Windows VM | Azure"
description: "如何通过 Resource Manager 部署模型使用 PowerShell 将新磁盘或现有数据磁盘附加到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
wacn.date: 
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: a114d832e9c5320e9a109c9020fcaa2f2fdd43a9
ms.openlocfilehash: 76e4a6ea03c2ba8a17e1daf00eca56531538cbe8
ms.lasthandoff: 04/14/2017


---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>使用 PowerShell 将数据磁盘附加到 Windows VM

本文介绍如何使用 PowerShell 将新磁盘和现有磁盘附加到 Windows 虚拟机。 如果 VM 使用托管磁盘，则可以附加其他托管数据磁盘。 此外还可以将非托管数据磁盘附加到存储帐户中使用非托管磁盘的 VM。

在开始之前，请查看以下提示：
* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要使用高级存储，需要支持高级存储的 VM 大小，如 DS 系列或 GS 系列虚拟机。 可以用高级存储帐户和标准存储帐户将磁盘用于这些虚拟机。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用的是最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令来安装该模块。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```

有关详细信息，请参阅 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>将空数据磁盘添加到虚拟机

此示例演示了如何将空数据磁盘添加到现有虚拟机。

### <a name="using-managed-disks"></a>使用托管磁盘

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West China North' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128

$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-unmanaged-disks-in-a-storage-account"></a>在存储帐户中使用非托管磁盘

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.chinacloudapi.cn/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```

### <a name="initialize-the-disk"></a>初始化磁盘

添加空磁盘后，需要对其进行初始化。 若要初始化磁盘，可以登录到 VM，然后使用磁盘管理进行初始化。 如果在创建 VM 时在其上启用了 WinRM 和证书，则可以通过远程 PowerShell 初始化该磁盘。 还可以使用自定义脚本扩展： 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

脚本文件可以包含类似如下所示代码初始化磁盘：

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM

还可以将现有 VHD 作为托管数据磁盘附加到虚拟机。 

### <a name="using-managed-disks"></a>使用托管磁盘

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West China North' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>后续步骤

创建[快照](virtual-machines-windows-snapshot-copy-managed-disk.md)。