---
title: 将 Linux 故障排除 VM 与 Azure CLI 配合使用 | Azure
description: 了解如何通过使用 Azure CLI 将 OS 磁盘连接到恢复 VM 来排查 Linux VM 问题
services: virtual-machines-linux
documentationCenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 02/16/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: ed27f4e110ae2e5f1bee91c7a48c42b8bb34d8e0
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004046"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>通过使用 Azure CLI 将 OS 磁盘附加到恢复 VM 来对 Linux VM 进行故障排除
如果 Linux 虚拟机 (VM) 遇到启动或磁盘错误，则可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是 `/etc/fstab` 中存在无效条目，使 VM 无法成功启动。 本文详细介绍如何使用 Azure CLI 将虚拟硬盘连接到另一个 Linux VM，以修复任何错误，然后重新创建原始 VM。 

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 删除遇到问题的 VM，保留虚拟硬盘。
2. 将虚拟硬盘附加并装入到另一个 Linux VM，以便进行故障排除。
3. 连接到故障排除 VM。 编辑文件或运行任何工具以修复原始虚拟硬盘上的问题。
4. 从故障排除 VM 卸载并分离虚拟硬盘。
5. 使用原始虚拟硬盘创建 VM。

有关使用托管磁盘的 VM，请参阅[通过附加新的操作系统磁盘对托管磁盘 VM 进行故障排除](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk)。

若要执行这些故障排除步骤，需要安装最新的 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-latest)，并使用 [az login](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

在以下示例中，请将参数名称替换成自己的值。 示例参数名称包括 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

## <a name="determine-boot-issues"></a>确定启动问题
检查串行输出以确定 VM 不能正常启动的原因。 一个常见示例是 `/etc/fstab`中存在无效条目，或底层虚拟硬盘已删除或移动。

使用 [az vm boot-diagnostics get-boot-log](https://docs.azure.cn/zh-cn/cli/vm/boot-diagnostics?view=azure-cli-latest#az-vm-boot-diagnostics-get-boot-log) 获取启动日志。 以下示例从名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 获取串行输出：

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

检查串行输出，以确定 VM 无法启动的原因。 如果串行输出未提供任何指示，则在将虚拟硬盘连接到故障排除 VM 后，可能需要查看 `/var/log` 中的日志文件。

## <a name="view-existing-virtual-hard-disk-details"></a>查看现有虚拟硬盘的详细信息
在将虚拟硬盘 (VHD) 附加到另一个 VM 之前，需要标识 OS 磁盘的 URI。 

使用 [az vm show](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-show) 查看有关 VM 的信息。 使用 `--query` 标志提取 OS 磁盘的 URI。 以下示例在名为 `myResourceGroup` 的资源组中获取名为 `myVM` 的 VM 的磁盘信息：

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

此 URI 类似于 **https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/myVM.vhd**。

## <a name="delete-existing-vm"></a>删除现有 VM
虚拟硬盘和 VM 在 Azure 中是两个不同的资源。 虚拟硬盘是操作系统本身，存储应用程序和配置。 VM 本身只是定义大小或位置的元数据，引用虚拟硬盘或虚拟网络接口卡 (NIC) 等资源。 每个虚拟硬盘在附加到 VM 时分配有一个租约。 尽管 VM 正在运行时也可以附加和分离数据磁盘，但是，若要分离 OS 磁盘，则必须删除 VM 资源。 即使 VM 处于停止和解除分配状态，租约也继续将 OS 磁盘与 VM 相关联。

恢复 VM 的第一步是删除 VM 资源本身。 删除 VM 时会将虚拟硬盘留在存储帐户中。 删除 VM 后，可将虚拟硬盘附加到另一个 VM，以排查和解决这些错误。

使用 [az vm delete](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-delete) 删除 VM。 以下示例从名为 `myResourceGroup` 的资源组中删除名为 `myVM` 的 VM：

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

等到 VM 已完成删除，再将虚拟硬盘附加到另一个 VM。 虚拟硬盘上将其与 VM 关联的租约需要释放，才能将虚拟硬盘附加到另一个 VM。

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>将现有虚拟硬盘附加到另一个 VM
在后续几个步骤中，使用另一个 VM 进行故障排除。 将现有虚拟硬盘附加到此故障排除 VM，以浏览和编辑磁盘的内容。 例如，此过程允许用户更正任何配置错误或者查看其他应用程序或系统日志文件。 选择或创建另一个 VM 以用于故障排除。

使用 [az vm unmanaged-disk attach](https://docs.azure.cn/zh-cn/cli/vm/unmanaged-disk?view=azure-cli-latest#az-vm-unmanaged-disk-attach)附加现有的虚拟硬盘。 附加现有的虚拟硬盘时，请指定在前面的 `az vm show` 命令中获取的磁盘 URI。 以下示例将现有的虚拟硬盘附加到名为 `myResourceGroup` 的资源组中名为 `myVMRecovery` 的故障排除 VM：

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/myVM.vhd
```

## <a name="mount-the-attached-data-disk"></a>装载附加的数据磁盘

> [!NOTE]
> 以下示例详细说明了在 Ubuntu VM 上需要执行的步骤。 如果使用不同的 Linux 发行版（如 CentOS 或 SUSE），日志文件位置和 `mount` 命令可能会稍有不同。 请参阅具体分发版的文档，了解命令中有哪些相应的变化。

<!-- Change Red Hat to CentOS -->

1. 使用相应的凭据通过 SSH 连接到故障排除 VM。 如果此磁盘是附加到故障排除 VM 的第一个数据磁盘，则此磁盘可能已连接到 `/dev/sdc`。 使用 `dmseg` 查看附加的磁盘：

    ```bash
    dmesg | grep SCSI
    ```

    输出类似于以下示例：

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    在前面的示例中，OS 磁盘位于 `/dev/sda`，为每个 VM 提供的临时磁盘位于 `/dev/sdb`。 如果有多个数据磁盘，它们应位于 `/dev/sdd`、`/dev/sde`，依次类推。

2. 创建一个目录来装载现有的虚拟硬盘。 以下示例创建一个名为 `troubleshootingdisk`的目录：

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 如果现有的虚拟硬盘上有多个分区，则装载所需的分区。 以下示例在 `/dev/sdc1` 中装载第一个主分区：

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 最佳做法是使用虚拟硬盘的全局唯一标识符 (UUID) 装载 Azure 中 VM 上的数据磁盘。 对于此简短的故障排除方案，不必要使用 UUID 装载虚拟硬盘。 但是，在正常使用时，编辑 `/etc/fstab` 以使用设备名称（而不是 UUID）装载虚拟硬盘可能会导致 VM 无法启动。

## <a name="fix-issues-on-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决错误后，可从故障排除 VM 中卸载并分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘用于任何其他 VM。

1. 通过 SSH 会话登录到故障排除 VM 中，卸载现有的虚拟硬盘。 首先更改出装入点的父目录：

    ```bash
    cd /
    ```

    现在卸载现有的虚拟硬盘。 以下示例卸载 `/dev/sdc1`中的设备：

    ```bash
    sudo umount /dev/sdc1
    ```

2. 现在从 VM 中分离虚拟硬盘。 退出故障排除 VM 的 SSH 会话。 使用 [az vm unmanaged-disk list](https://docs.azure.cn/zh-cn/cli/vm/unmanaged-disk?view=azure-cli-latest#az-vm-unmanaged-disk-list) 列出附加到故障排除 VM 的数据磁盘。 以下示例列出附加到名为 `myResourceGroup` 的资源组中名为 `myVMRecovery` 的 VM 的数据磁盘：

    ```azurecli
    az vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    记下现有虚拟硬盘的名称。 例如，URI 为 **https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/myVM.vhd** 的磁盘的名称是 **myVHD**。 

    使用 [az vm unmanaged-disk detach](https://docs.azure.cn/zh-cn/cli/vm/unmanaged-disk?view=azure-cli-latest#az-vm-unmanaged-disk-detach) 将数据磁盘从 VM 中分离。 以下示例将名为 `myVHD` 的磁盘从 `myResourceGroup` 资源组中名为 `myVMRecovery` 的 VM 中分离：

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="create-vm-from-original-hard-disk"></a>从原始硬盘创建 VM
若要从原始虚拟硬盘创建 VM，请使用 [此 Azure Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)。 实际的 JSON 模板位于以下链接中：

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

该模板使用此前的命令中的 VHD URI 部署 VM。 使用 [az group deployment create](https://docs.azure.cn/zh-cn/cli/group/deployment?view=azure-cli-latest#az-group-deployment-create)部署模板。 将 URI 提供给原始 VHD，然后指定 OS 类型、VM 大小、VM 名称，如下所示：

> [!NOTE]
> 必须修改从 GitHub 存储库“azure-quickstart-templates”下载或参考的模板，以适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“cloudapp.chinacloudapi.cn”）；必要时更改某些不受支持的位置、VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json
```

<!--Notice: template-uri should be https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json-->

## <a name="re-enable-boot-diagnostics"></a>重新启用启动诊断
从现有虚拟硬盘创建 VM 时，启动诊断可能不会自动启用。 使用 [az vm boot-diagnostics enable](https://docs.azure.cn/zh-cn/cli/vm/boot-diagnostics?view=azure-cli-latest#az-vm-boot-diagnostics-enable) 启用启动诊断。 以下示例在名为 `myResourceGroup` 的资源组中名为 `myDeployedVM` 的 VM 上启用诊断扩展：

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>通过附加新的操作系统磁盘对托管磁盘 VM 进行故障排除
1. 停止受影响的 VM。
2. [创建托管磁盘 VM 的操作系统磁盘的托管磁盘快照](../linux/snapshot-copy-managed-disk.md)。
3. [从快照创建托管磁盘](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md)。
4. [将托管磁盘附加为 VM 的数据磁盘](../windows/attach-disk-ps.md)。
5. [将步骤 4 中的数据磁盘更改为操作系统磁盘](../windows/os-disk-swap.md)。

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[排查 Azure VM 的 SSH 连接问题](troubleshoot-ssh-connection.md)。 如果在访问 VM 上运行的应用时遇到问题，请参阅[排查 Linux VM 上的应用程序连接问题](troubleshoot-app-connection.md)。

<!-- Update_Description: update meta propreties, wording update, update link -->
