---
title: 捕获 Linux VM 的映像 | Azure
description: 了解如何使用经典部署模型捕获基于 Linux 的 Azure 虚拟机 (VM) 的映像。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
origin.date: 03/14/2017
ms.date: 07/30/2018
ms.author: v-yeche
ms.openlocfilehash: 1e7f378f3881a9a2bb760c4af3a47437823173be
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643734"
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>如何捕获经典 Linux 虚拟机以用作映像
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 了解如何[使用 Resource Manager 模型执行这些步骤](../capture-image.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

本文将演示如何捕获运行 Linux 的经典 Azure 虚拟机 (VM) 作为创建其他虚拟机的映像。 此映像包括操作系统磁盘和附加到 VM 的数据磁盘。 它不包括网络配置，因此在使用此映像创建其他 VM 时需要进行网络配置。

Azure 在“映像”下存储映像 ，以及任何已上传的映像。 有关映像的详细信息，请参阅 [关于 Azure 中的虚拟机映像][About Virtual Machine Images in Azure]。

## <a name="before-you-begin"></a>准备阶段
这些步骤假定已使用经典部署模型创建了 Azure VM 并配置了操作系统，包括附加任何数据磁盘。 如果需要创建 VM，请阅读 [如何创建 Linux 虚拟机][How to Create a Linux Virtual Machine]。

## <a name="capture-the-virtual-machine"></a>捕获虚拟机
1. 使用所选 SSH 客户端[连接到 VM](../mac-create-ssh-keys.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。
2. 在 SSH 窗口中，键入以下命令。 `waagent` 的输出结果可能会因此实用程序的版本而略有差异：

    ```bash
    sudo waagent -deprovision+user
    ```

    前面的命令尝试清除系统并使其适用于重新预配。 此操作执行以下任务：

   * 删除 SSH 主机密钥（如果在配置文件中 Provisioning.RegenerateSshHostKeyPair 为“y”）
   * 清除 /etc/resolv.conf 中的 nameserver 配置
   * 从 /etc/shadow 中删除 `root` 用户的密码（如果在配置文件中 Provisioning.DeleteRootPassword 为“y”）
   * 删除缓存的 DHCP 客户端租赁
   * 将主机名重置为 localhost.localdomain
   * 删除上次预配的用户帐户（从 /var/lib/waagent 获得）**和关联的数据**。

     > [!NOTE]
     > 取消预配会删除文件和数据，目的是使映像“通用化”。 仅在要捕获为新映像模板的 VM 上运行此命令。 它无法保证映像中的所有敏感信息均已清除，或无法保证该映像适合再分发给第三方。

3. 键入 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
4. 键入 **Exit** 关闭 SSH 客户端。

   > [!NOTE]
   > 剩余步骤假定已在客户端计算机上[安装 Azure CLI](../../../cli-install-nodejs.md)。 以下所有步骤也可在 [Azure 门户](http://portal.azure.cn)中执行。

5. 从客户端计算机中打开 Azure CLI 并登录到 Azure 订阅。 有关详细信息，请阅读[从 Azure CLI 连接到 Azure 订阅](https://docs.azure.cn/zh-cn/cli/authenticate-azure-cli?view=azure-cli-latest)。

   > [!NOTE]
   > 登录到 Azure 门户。

6. 请确保处于服务管理模式下：

    ```azurecli
    azure config mode asm
    ```

7. 关闭已取消预配的 VM。 以下示例关闭名为 `myVM` 的 VM：

    ```azurecli
    azure vm shutdown myVM
    ```
   如果需要，可使用 `azure vm list` 查看在订阅中创建的所有 VM 列表

   > [!NOTE]
   > 如果在使用 Azure 门户，选择 VM 并单击“停止”即可关闭 VM。

8. 停止 VM 后，捕获映像。 以下示例捕获名为 `myVM` 的 VM，并创建名为 `myNewVM` 的通用映像：

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` 子命令将删除原始虚拟机。

    > [!NOTE]
    > 在 Azure 门户中，可以从中心菜单中选择“映像”以捕获映像。 需提供以下映像信息：名称、资源组、位置、操作系统类型和存储 blob 路径。

9. 新映像现在会出现在映像列表中，可以用于配置任何新的 VM。 可以使用以下命令来查看它：

   ```azurecli
   azure vm image list
   ```

   在 [Azure 门户](http://portal.azure.cn)中，新映像会出现在属于“计算”服务的“VM 映像(经典)”中。 可通过在 Azure 服务列表顶部单击“全部服务”，并在“计算”服务中查找来访问“VM 映像(经典)”。   

   ![成功捕获映像](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>后续步骤
该映像已就绪，可用于创建 VM 了。 可以使用 Azure CLI 命令 `azure vm create` 并提供所创建的映像名称。 有关详细信息，请参阅[将 Azure CLI 与经典部署模型配合使用](https://docs.azure.cn/zh-cn/cli/get-started-with-az-cli2?view=azure-cli-latest)。

此外，也可以使用 [Azure 门户](http://portal.azure.cn)创建自定义 VM，方法是使用**映像**方法并选择所创建的映像。 有关详细信息，请参阅[如何创建自定义 VM][How to Create a Custom Virtual Machine]。

**另请参阅：**[Azure Linux 代理用户指南](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md

<!-- Update_Description: update meta properties  -->
