---
title: Azure Stack 中托管磁盘和托管映像的差异与注意事项 | Microsoft Docs
description: 了解 Azure Stack 中托管磁盘和托管映像的差异与注意事项。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/23/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: cc3fe55f29d1449aff5f789383607fd8e1db95b8
ms.sourcegitcommit: 77d6ceb6a14a3316a6088859c4d9978115b2454a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248525"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 托管磁盘：差异与注意事项

本文汇总了 [Azure Stack 托管磁盘](azure-stack-manage-vm-disks.md)与 [Azure 托管磁盘](/virtual-machines/windows/managed-disks-overview)之间的已知差异。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

托管磁盘通过管理与 VM 磁盘关联的[存储帐户](../operator/azure-stack-manage-storage-accounts.md)简化了 IaaS VM 的磁盘管理。

> [!Note]  
> 从 1808 更新开始，推出了 Azure Stack 上的托管磁盘。 从 1811 更新开始，使用 Azure Stack 门户创建虚拟机时，默认会启用托管磁盘。
  
## <a name="cheat-sheet-managed-disk-differences"></a>速查表：托管磁盘的差异

| 功能 | Azure（中国） | Azure Stack |
| --- | --- | --- |
|静态数据加密 |Azure 存储服务加密 (SSE)、Azure 磁盘加密 (ADE)     |BitLocker 128 位 AES 加密      |
|映像          | 支持托管自定义映像 |支持|
|备份选项 |支持 Azure 备份服务 |尚不支持 |
|灾难恢复选项 |支持 Azure Site Recovery |尚不支持|
|磁盘类型     |高级 SSD、标准 SSD 和标准 HDD |高级 SSD、标准 HDD |
|高级磁盘  |完全支持 |可部署，但无性能限制或保证  |
|高级磁盘 IOPS  |取决于磁盘大小  |每个磁盘 2300 IOPS |
|高级磁盘吞吐量 |取决于磁盘大小 |每个磁盘 145 MB/秒 |
|磁盘大小  |Azure 高级磁盘：P4 (32 GiB) 到 P80 (32 TiB)<br>Azure 标准 SSD 磁盘：E10 (128 GiB) 到 E80 (32 TiB)<br>Azure 标准 HDD 磁盘：S4 (32 GiB) 到 S80 (32 TiB) |M4：32 GiB<br>M6：64 GiB<br>M10：128 GiB<br>M15：256 GiB<br>M20：512 GiB<br>M30：1024 GiB |
|磁盘快照复制|支持附加到正在运行的 VM 的快照 Azure 托管磁盘|尚不支持 |
|磁盘性能分析 |支持的聚合指标和每磁盘指标 |尚不支持 |
|迁移      |提供从现有非托管 Azure 资源管理器 VM 迁移的工具，而无需重新创建 VM  |尚不支持 |

> [!NOTE]  
> Azure Stack 中的托管磁盘 IOPs 和吞吐量是一个上限数字而非预配的数字，这可能会受在 Azure Stack 中运行的硬件和工作负荷影响。

## <a name="metrics"></a>指标

存储指标也有一些差异：

- 使用 Azure Stack，存储指标中的事务数据不区分内部或外部网络带宽。
- 存储指标中的 Azure Stack 事务数据不包含虚拟机对所装载磁盘的访问。

## <a name="api-versions"></a>API 版本

Azure Stack 托管磁盘支持以下 API 版本：

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>转换为托管磁盘

可以使用以下脚本将当前预配的 VM 从非托管磁盘转换为托管磁盘。 将占位符替换成自己的值：

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>托管映像

Azure Stack 支持托管映像，可让你在通用化 VM（非托管和托管的 VM）上创建托管映像对象，以后只能创建托管磁盘 VM。  托管映像可实现以下两种方案：

- 你有通用化的非托管 VM，后来想要使用托管磁盘。
- 你有通用化的托管 VM，并想要创建多个类似的托管 VM。

### <a name="step-1-generalize-the-vm"></a>步骤 1：通用化 VM

对于 Windows，请遵循[使用 Sysprep 通用化 Windows VM](/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) 部分操作。 对于 Linux，请遵循[此处](/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)所述的步骤 1。

> [!NOTE]
> 请务必将 VM 通用化。 基于未正确通用化的映像创建 VM 会导致 **VMProvisioningTimeout** 错误。

### <a name="step-2-create-the-managed-image"></a>步骤 2：创建托管映像

可以使用门户、PowerShell 或 CLI 创建托管映像。 请遵循[此](/virtual-machines/windows/capture-image-resource) Azure 文章中的步骤操作。

### <a name="step-3-choose-the-use-case"></a>步骤 3：选择用例

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>情况 1：将非托管 VM 迁移到托管磁盘

执行此步骤之前，请务必正确通用化 VM。 通用化之后，不再可以使用此 VM。 基于未正确通用化的映像创建 VM 会导致 **VMProvisioningTimeout** 错误。

遵照[此处](/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account)的说明，从存储帐户中的通用化 VHD 创建托管映像。 以后可以使用此映像创建托管 VM。

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>情况 2：使用 PowerShell 基于托管映像创建托管 VM

使用[此处](/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)的脚本从现有托管磁盘 VM 创建映像之后，以下示例脚本从现有映像对象创建类似的 Linux VM：

Azure Stack PowerShell 模块 1.7.0 或更高版本：请遵照[此处](/virtual-machines/windows/create-vm-generalized-managed)的说明操作。

Azure Stack PowerShell 模块 1.6.0 或更低版本：

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "ChinaEast"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

也可以使用门户基于托管映像创建 VM。 有关详细信息，请参阅 Azure 托管映像文章：[在 Azure 中创建通用化 VM 的托管映像](/virtual-machines/windows/capture-image-resource)和[从托管映像创建 VM](/virtual-machines/windows/create-vm-generalized-managed)。

## <a name="configuration"></a>配置

应用 1808 或更高版本的更新后，必须先执行以下配置才能使用托管磁盘：

- 如果订阅是在应用 1808 更新之前创建的，请遵循以下步骤来更新订阅。 否则，在此订阅中部署 VM 可能会失败，并出现错误消息“磁盘管理器发生内部错误。”
   1. 在租户门户中转到“订阅”，找到相应订阅。  依次单击“资源提供程序”、“Microsoft.Compute”  、“重新注册”  。 
   2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。  
- 如果使用多租户环境，请让云操作员（可以是组织内部或来自服务提供商的操作员）根据[此文](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤重新配置每个来宾目录。 否则，在与该来宾目录关联的订阅中部署 VM 可能会失败，并出现错误消息“磁盘管理器中发生内部错误。”

## <a name="next-steps"></a>后续步骤

- [了解 Azure Stack 虚拟机](azure-stack-compute-overview.md)
