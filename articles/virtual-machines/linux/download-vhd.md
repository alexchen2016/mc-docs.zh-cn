---
title: 从 Azure 下载 Linux VHD | Azure
description: 使用 Azure CLI 和 Azure 门户下载 Linux VHD。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 06/01/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 70a8afcaa60137079c34e3013a0a47484f5ccebe
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627176"
---
# <a name="download-a-linux-vhd-from-azure"></a>从 Azure 下载 Linux VHD

本文介绍如何使用 Azure CLI 和 Azure 门户从 Azure 下载 Linux 虚拟硬盘 (VHD) 文件。 

如果尚未安装 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-latest)，请安装。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 需要停止 VM，才能下载 VHD。 如果要使用 VHD 作为[映像](tutorial-custom-images.md)创建包含新磁盘的其他 VM，则需要取消设置并通用化文件中包含的操作系统，然后停止 VM。 若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，只需停止并解除分配 VM。

若要使用 VHD 作为映像创建其他 VM，请完成以下步骤：

1. 使用 SSH、帐户名称和 VM 的公共 IP 地址连接到它并对其取消设置。 可以使用 [az network public-ip show](https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) 查找公共 IP 地址。 +user 参数还会删除上次预配的用户帐户。 如果要将帐户凭据收录到 VM，请省略此 +user 参数。 以下示例删除上次预配的用户帐户：

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. 使用 [az login](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。
3. 停止并解除分配 VM。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. 通用化 VM。 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，请完成以下步骤：

1.  登录到 [Azure 门户](https://portal.azure.cn/)。
2.  在“中心”菜单中，单击“虚拟机” 。
3.  从列表中选择 VM。
4.  在 VM 的边栏选项卡上，单击“停止”。

    ![停止 VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>生成 SAS URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1.  在 VM 的边栏选项卡的菜单上，单击“磁盘”。
2.  为 VM 选择操作系统磁盘，然后单击“导出”。
3.  单击“生成 URL”。

    ![生成 URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>下载 VHD

1.  在生成的 URL 下，单击“下载 VHD 文件”。

    ![下载 VHD](./media/download-vhd/export-download.png)

2.  可能需要单击浏览器中的“保存”以开始下载。 VHD 文件的默认名称为 *abcd*。

    ![单击浏览器中的“保存”](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure CLI 从自定义磁盘上传并创建 Linux VM](upload-vhd.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。 
- [使用 Azure CLI 管理 Azure 磁盘](tutorial-manage-disks.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。

<!-- Update_Description: update meta properties， wording update -->