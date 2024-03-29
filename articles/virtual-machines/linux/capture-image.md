---
title: 使用 Azure CLI 捕获 Azure 中的 Linux VM 的映像 | Azure
description: 使用 Azure CLI 捕获 Azure VM 的映像以用于批量部署。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
origin.date: 10/08/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: ffd27a0ac3f4b5d6facf9918526b1a49253faa28
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56665853"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>如何创建虚拟机或 VHD 的映像

<!-- generalize, image - extended version of the tutorial-->

若要创建虚拟机 (VM) 的多个副本以在 Azure 中使用，需要捕获 VM 或 OS VHD 的映像。 若要创建用于部署的映像，你将需要删除个人帐户信息。 在下列步骤中，你取消预配现有 VM，将其解除分配并创建映像。 可以使用此映像，在订阅内的任何资源组中创建 VM。

若要创建现有 Linux VM 的副本以用于备份或调试，或从本地 VM 上传专用 Linux VHD，请参阅[上传自定义磁盘映像并根据该映像创建 Linux VM](upload-vhd.md)。  

还可使用 **Packer** 创建自定义配置。 有关详细信息，请参阅[如何使用 Packer 在 Azure 中创建 Linux 虚拟机映像](build-image-with-packer.md)。

在创建映像前，需要具有以下项：

* 在资源管理器部署模型中使用托管磁盘创建的 Azure VM。 如果尚未创建 Linux VM，则可以使用[门户](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或[资源管理器模板](create-ssh-secured-vm-from-template.md)。 根据需要配置 VM。 例如， [添加数据磁盘](add-disk.md)、应用更新和安装应用程序。 

* 已安装最新 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-latest)，并已使用 [az login](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="quick-commands"></a>快速命令

若要获得本文的简化版本，并用于测试、评估或了解 Azure 中的 VM，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)。

## <a name="step-1-deprovision-the-vm"></a>步骤 1：取消预配 VM
首先，使用 Azure VM 代理取消预配 VM 以删除计算机特定文件和数据。 在源 Linux VM 上，将 `waagent` 命令与 `-deprovision+user` 参数配合使用。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../extensions/agent-linux.md)。

1. 使用 SSH 客户端连接到 Linux VM。
2. 在 SSH 窗口中，输入以下命令：

    ```bash
    sudo waagent -deprovision+user
    ```
    > [!NOTE]
    > 请仅在要捕获为映像的 VM 上运行此命令。 此命令不保证映像中的所有敏感信息被清除，或者映像适合用于分发。 `+user` 参数还会删除上次预配的用户帐户。 若要将用户帐户凭据保留在 VM 中，请仅使用 `-deprovision`。

3. 按 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
4. 在命令完成后，输入 **exit** 以关闭 SSH 客户端。

## <a name="step-2-create-vm-image"></a>步骤 2：创建 VM 映像
使用 Azure CLI 将 VM 标记为通用化并捕获映像。 在以下示例中，请将示例参数名称替换成自己的值。 示例参数名称包括 *myResourceGroup*、*myVnet* 和 *myVM*。

1. 对使用 [az vm deallocate](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-deallocate) 取消设置的 VM 解除分配。 以下示例在名为 *myResourceGroup* 的资源组中解除分配名为 *myVM* 的 VM。

    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. 使用 [az vm generalize](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-generalize) 将 VM 标记为通用化。 以下示例将名为 *myResourceGroup* 的资源组中名为 *myVM* 的 VM 标记为通用化的。

    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. 使用 [az image create](https://docs.azure.cn/zh-cn/cli/image?view=azure-cli-latest#az-image-create) 创建 VM 资源的映像。 以下示例使用名为 *myVM* 的 VM 资源在名为 *myResourceGroup* 的资源组中创建名为 *myImage* 的映像。

    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```

    > [!NOTE]
    > 该映像在与源 VM 相同的资源组中创建。 可以在订阅内的任何资源组中从此映像创建 VM。 从管理角度来看，你可能希望为 VM 资源和映像创建特定的资源组。

    <!-- Not Available on availability zones -->

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步骤 3：从捕获的映像创建 VM
通过 [az vm create](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-create) 使用你已创建的映像创建 VM。 以下示例基于名为 *myImage* 的映像创建名为 *myVMDeployed* 的 VM。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>在另一个资源组中创建 VM 

可在订阅内的任何资源组中根据映像创建 VM。 要在与映像不同的资源组中创建 VM，请指定映像的完整资源 ID。 使用 [az image list](https://docs.azure.cn/zh-cn/cli/image?view=azure-cli-latest#az-image-list) 查看映像列表。 输出类似于以下示例。

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "chinanorth",
   "name": "myImage",
```

以下示例使用 [az vm create](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-create)，通过指定映像资源 ID，在与源映像不同的资源组中创建 VM。

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="step-4-verify-the-deployment"></a>步骤 4：验证部署

通过 SSH 连接到你创建的虚拟机以验证部署并开始使用新的 VM。 若要通过 SSH 进行连接，请使用 [az vm show](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-show)查找 VM 的 IP 地址或 FQDN。

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>后续步骤
可以从源 VM 映像创建多个 VM。 若要对映像进行更改，请执行以下操作： 

- 从映像创建 VM。
- 进行任何更新或配置更改。
- 再次执行相关步骤，对 VM 执行取消预配、解除分配、通用化和创建操作。
- 将此新映像用于将来的部署。 可以删除原始映像。

有关使用 CLI 管理 VM 的详细信息，请参阅 [Azure CLI](https://docs.azure.cn/zh-cn/cli/index?view=azure-cli-latest)。

<!-- Update_Description: update meta properties, wording update, update az cmdlet -->
