---
title: VM 启动时停滞，显示消息“正在准备 Windows。 请不要关闭计算机。” | Azure
description: 介绍当 VM 启动停滞并显示“正在准备 Windows。 请不要关闭计算机。”时用来解决问题的步骤。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 09/18/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 80735e9e07d267a2082996eb2202f34d815d1243
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674138"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 启动时停滞，显示消息“正在准备 Windows。 请不要关闭计算机。” ”

本文可帮助你解决当虚拟机 (VM) 启动停滞并显示“正在准备 Windows。 请不要关闭计算机。” 消息时出现的问题。

## <a name="symptoms"></a>症状

使用**启动诊断**获取 VM 的屏幕截图时，发现操作系统没有完全启动。 另外，VM 显示了消息“正在准备 Windows。请不要关闭计算机。” 。

![消息示例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![消息示例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

当服务器在配置发生更改后执行最终的重启时，通常会发生此问题。 配置更改无法通过 Windows 更新进行初始化，或者无法通过更改服务器的角色/功能进行初始化。 对于 Windows 更新，如果更新较大，则操作系统需要更多时间来重新配置更改。

## <a name="back-up-the-os-disk"></a>备份 OS 磁盘

在尝试解决该问题之前，请备份 OS 磁盘：

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>对于有加密磁盘的 VM，必须先解锁磁盘

验证 VM 是否为加密的 VM。 为此，请执行以下步骤：

1. 在门户中打开 VM，然后浏览到磁盘。

2. 此时会看到一个名为“加密”的列，它会指出是否已启用加密。

如果 OS 磁盘已加密，请解锁加密的磁盘。 为此，请执行以下步骤：

1. 创建一个恢复 VM，该 VM 与受影响的 VM 位于相同的资源组、存储帐户和位置中。

2. 在 Azure 门户中，删除受影响的 VM 但保留磁盘。

3. 以管理员身份运行 PowerShell。

4. 运行以下 cmdlet，获取机密名称。

    ```Powershell
    Login-AzureRmAccount -EnvironmentName AzureChinaCloud

    $vmName = "VirtualMachineName"
    $vault = "AzureKeyVaultName"

    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq "C:\") -and ($_.ContentType -eq 'BEK')}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq 'BEK')}
    ```

5. 获得机密名称后，在 PowerShell 中运行以下命令：

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. 将 Base64 编码的值转换为字节并将输出写入到一个文件中。 

    > [!Note]
    > 如果使用 USB 解锁选项，则 BEK 文件名必须与原始 BEK GUID 匹配。 此外，需要在 C 驱动器上创建一个名为“BEK”的文件夹，然后才能执行以下步骤。

    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "c:\BEK\$secretName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. 在电脑上创建 BEK 文件后，将该文件复制到附加了锁定 OS 磁盘的恢复 VM。 使用 BEK 文件位置运行以下命令：

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **可选** 在某些情况下，可能还需要使用以下命令来解密磁盘。

    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 此命令假定要解密的磁盘的盘符为 F:。

8. 如果需要收集日志，可以导航到路径 **盘符:\Windows\System32\winevt\Logs**。

9. 将驱动器与恢复计算机分离。

### <a name="create-a-snapshot"></a>创建快照

若要创建快照，请执行[创建磁盘快照](..\windows\snapshot-copy-managed-disk.md)中的步骤。

## <a name="collect-an-os-memory-dump"></a>收集 OS 内存转储

当 VM 在配置阶段停滞时，请使用[收集 OS 转储](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)部分的步骤来收集 OS 转储。

## <a name="contact-azure-support"></a>联系 Azure 支持部门

收集转储文件后，请联系 [Azure 支持部门](https://www.azure.cn/support/support-azure/)来分析根本原因。

## <a name="rebuild-the-vm-using-powershell"></a>使用 PowerShell 重新生成 VM

收集内存转储文件后，请使用以下步骤重新生成 VM。

**对于非托管磁盘**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount -EnvironmentName AzureChinaCloud

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription -SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**对于托管磁盘**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount -EnvironmentName AzureChinaCloud

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription -SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet -Name $avName -ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```

<!-- Update_Description: update meta properties, wording update -->