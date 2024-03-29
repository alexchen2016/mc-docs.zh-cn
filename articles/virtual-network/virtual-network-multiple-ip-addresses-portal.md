---
title: Azure 虚拟机的多个 IP 地址 - 门户 | Azure
description: 了解如何使用 Azure 门户 | Resource Manager 将多个 IP 地址分配给虚拟机。
services: virtual-network
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 11/30/2016
ms.date: 05/07/2018
ms.author: v-yeche
ms.openlocfilehash: 16cedd8d1d74102f9a0adb41ebc047a52c129d6a
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625488"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>使用 Azure 门户将多个 IP 地址分配给虚拟机

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> 本文介绍如何使用 Azure 门户通过 Azure Resource Manager 部署模型创建虚拟机 (VM)。 无法将多个 IP 地址分配到通过经典部署模型创建的资源。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>创建具有多个 IP 地址的 VM

若要创建具有多个 IP 地址或一个静态专用 IP 地址的 VM，则必须使用 PowerShell 或 Azure CLI 来创建。 相关操作步骤，请单击本文顶部的 PowerShell 或 CLI 选项。 可使用单个动态专用 IP 地址和（可选）单个公共 IP 地址创建 VM。 请按照[创建 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[创建 Linux VM](../virtual-machines/linux/quick-create-portal.md) 文章中的步骤使用门户。 创建 VM 后，可以按照本文[向 VM 添加 IP 地址](#add)部分中的以下步骤，可使用门户将 IP 地址类型从动态更改为静态并添加其他 IP 地址。

<a name="add"></a>
## <a name="add-ip-addresses-to-a-vm"></a>将 IP 地址添加到 VM

完成以下步骤可将专用和公共 IP 地址添加到 Azure 网络接口。 以下部分中的示例假定你的 VM 已具备[方案](#Scenario)中所属的三个 IP 配置（但此要求不是必备要求）。

<a name="coreadd"></a>
### <a name="core-steps"></a>核心步骤

1. 根据需要浏览到 Azure 门户（网址为 https://portal.azure.cn）并登录。
2. 在门户中单击“更多服务”> 在筛选框中键入“虚拟机”，并单击“虚拟机”。
3. 在“虚拟机”窗格中，单击要向其添加 IP 地址的 VM。 在显示的“虚拟机”窗格中单击“网络接口”，并选择要向其添加 IP 地址的网络接口。 在下图所示的示例中，已从名为“myVM”的 VM 中选定名为“myNIC”的 NIC：

    ![Linux](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. 在针对所选 NIC 显示的窗格中，单击“IP 配置”。

根据要添加的 IP 地址的类型完成以下某个部分的步骤。

### <a name="add-a-private-ip-address"></a>**添加专用 IP 地址**

完成以下步骤，添加新的专用 IP 地址：

1. 完成本文 [核心步骤](#coreadd) 部分的步骤。
2. 单击“添加” 。 在显示的“添加 IP 配置”窗格中，创建名为“IPConfig-4”的 IP 配置，以“10.0.0.7”作为“静态”专用 IP 地址，并单击“确定”。

    > [!NOTE]
    > 在添加静态 IP 地址时，必须在 NIC 连接到的子网中指定未使用的有效地址。 如果所选地址不可用，门户会对 IP 地址显示一个 X，而你必须选择其他地址。

3. 单击“确定”后，窗格将关闭且列出新的 IP 配置。 单击“确定”关闭“添加 IP 配置”窗格。
4. 可以单击“添加”添加其他 IP 配置，也可以关闭所有打开的边栏选项卡，完成添加 IP 地址的操作。
5. 通过完成本文内[将 IP 地址添加到 VM 操作系统](#os-config)部分中的步骤，将专用 IP 地址添加到 VM 操作系统。

### <a name="add-a-public-ip-address"></a>添加公共 IP 地址

将公共 IP 地址资源关联到新 IP 配置或现有 IP 配置即可添加公共 IP 地址。

> [!NOTE]
> 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://www.azure.cn/pricing/details/reserved-ip-addresses/)页。 可在一个订阅中使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。
> 

<a name="create-public-ip"></a>
### <a name="create-a-public-ip-address-resource"></a>创建公共 IP 地址资源

公共 IP 地址是针对公共 IP 地址资源的一种设置。 如果公共 IP 地址资源目前所关联到的 IP 配置不是你想要关联到的 IP 配置，可跳过以下步骤，根据需要完成后面某个部分的步骤。 如果没有可用的公共 IP 地址资源，可通过以下步骤创建一个：

1. 根据需要浏览到 Azure 门户（网址为 https://portal.azure.cn）并登录。
3. 在门户中，单击“创建资源” > “网络” > “公共 IP 地址”。
4. 在显示的“创建公共 IP 地址”窗格中，再输入名称，选择“IP 地址分配”类型、“订阅”、“资源组”和“位置”，然后单击“创建”，如下图所示：

    ![创建公共 IP 地址资源](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 完成下面某个部分的步骤，将公共 IP 地址资源关联到某个 IP 配置。

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>将公共 IP 地址资源关联到新 IP 配置

1. 完成本文[核心步骤](#coreadd)部分的步骤。
2. 单击“添加” 。 在显示的“添加 IP 配置”窗格中，创建名为“IPConfig-4”的 IP 配置。 启用“公共 IP 地址”，并从显示的“选择公共 IP 地址”窗格中选择一个现有的可用公共 IP 地址资源。

    在选择公共 IP 地址资源后单击“确定”，窗格随即关闭。 如果还没有公共 IP 地址，则可通过完成本文[创建公共 IP 地址资源](#create-public-ip)部分的步骤创建一个。 

3. 查看新 IP 配置。 即使没有向 IP 配置显式分配专用 IP 地址，也会自动分配一个，因为所有 IP 配置都必须有一个专用 IP 地址。
4. 可以单击“添加”添加其他 IP 配置，也可以关闭所有打开的边栏选项卡，完成添加 IP 地址的操作。
5. 将专用 IP 地址添加到 VM 操作系统，只需完成本文 [将 IP 地址添加到 VM 操作系统](#os-config) 部分针对操作系统的步骤即可。 请勿向操作系统添加公共 IP 地址。

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>将公共 IP 地址资源关联到现有 IP 配置

1. 完成本文[核心步骤](#coreadd)部分的步骤。
2. 单击其中要添加公共 IP 地址资源的 IP 配置。
3. 在出现的“IPConfig”窗格中，单击“IP 地址”。
4. 在出现的“选择公共 IP 地址”窗格中，选择公共 IP 地址。
5. 单击“保存”，窗格随即关闭。 如果还没有公共 IP 地址，则可通过完成本文[创建公共 IP 地址资源](#create-public-ip)部分的步骤创建一个。
3. 查看新 IP 配置。
4. 可以单击“添加”添加其他 IP 配置，也可以关闭所有打开的边栏选项卡，完成添加 IP 地址的操作。 请勿向操作系统添加公共 IP 地址。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

<!-- Update_Description: update meta properties, wording update， update link -->