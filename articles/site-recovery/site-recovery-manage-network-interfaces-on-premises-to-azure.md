---
title: 使用 Azure Site Recovery 管理网络接口，以实现本地到 Azure 的灾难恢复 | Azure
description: 介绍如何使用 Azure Site Recovery 管理网络接口，以实现本地到 Azure 的灾难恢复
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: conceptual
origin.date: 04/09/2019
ms.date: 06/10/2019
ms.author: v-yeche
ms.openlocfilehash: 46b176b068665f788d0ca0be5d057d443f9ade1a
ms.sourcegitcommit: 440d53bb61dbed39f2a24cc232023fc831671837
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390721"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>管理用于本地到 Azure 灾难恢复的虚拟机网络接口
Azure 中的虚拟机 (VM) 必须附加有至少一个网络接口。 它可以附加有 VM 的大小所能够支持的网络接口数量。

默认情况下，Azure 虚拟机上附加的第一个网络接口定义为主网络接口。 虚拟机中的所有其他网络接口为辅助网络接口。 另外，默认情况下，来自虚拟机的所有出站流量都是通过分配给主网络接口的主 IP 配置的 IP 地址发出的。

在本地环境中，虚拟机或服务器可以有多个网络接口，适用于环境中的不同网络。 不同的网络通常用于执行特定的操作，例如升级、维护和 Internet 访问。 从本地环境迁移或故障转移到 Azure 时，请记住，同一虚拟机中的网络接口必须全部连接到同一虚拟网络。

默认情况下，Azure Site Recovery 在 Azure 虚拟机上创建的网络接口数量与连接到本地服务器的网络接口数量相同。 通过编辑已复制虚拟机的设置下的网络接口设置，可避免在迁移或故障转移过程中创建冗余网络接口。

## <a name="select-the-target-network"></a>选择目标网络

对于 VMware 和物理机，以及 Hyper-V（不带 System Center Virtual Machine Manager）虚拟机，可为单个虚拟机指定目标虚拟网络。 对于通过 Virtual Machine Manager 托管的 Hyper-V 虚拟机，请使用[网络映射](site-recovery-network-mapping.md)映射某个源 Virtual Machine Manager 服务器上的 VM 网络，并以 Azure 网络为目标。

1. 在恢复服务保管库中的“复制的项”  下，选择任何复制的项即可访问其设置。

2. 选择“计算和网络”  选项卡可访问已复制项的网络设置。

3. 在“网络属性”  下的可用网络接口的列表中，选择一个虚拟网络。

    ![网络设置](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

修改目标网络会影响该特定虚拟机的所有网络接口。

对于 Virtual Machine Manager 云，修改网络映射会影响所有虚拟机及其网络接口。

## <a name="select-the-target-interface-type"></a>选择目标接口类型

在“计算和网络”  窗格中的“网络接口”  部分下，可查看和编辑网络接口设置。 还可指定目标网络接口类型。

- 故障转移需使用**主**网络接口。
- 所有其他选定的网络接口（若有）为**辅助**网络接口。
- 选择“不使用”  可避免在故障转移时创建某个网络接口。

默认情况下，启用复制时，Site Recovery 会选择本地服务器上所有检测到的网络接口。 它会将其中一个标记为“主要”  ，所有其他的标记为“辅助”  。 本地服务器上后续添加的任何接口均默认标记为“不使用”  。 添加更多网络接口时，请确保选择正确的 Azure 虚拟机目标大小，使之可容纳所有必需的网络接口。

## <a name="modify-network-interface-settings"></a>修改网络接口设置

可修改复制项网络接口的子网和 IP 地址。 如果未指定 IP 地址，则 Site Recovery 会在故障转移时将下一个可用 IP 地址从子网分配到网络接口。

1. 选择任何可用的网络接口以打开网络接口设置。

2. 从可用子网的列表中选择所需子网。

3. 输入适当的 IP 地址（根据需要）。

    ![网络接口设置](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. 选择“确定”  以完成编辑，然后返回“计算和网络”  窗格。

5. 至于其他网络接口，请重复步骤 1-4。

6. 选择“保存”  ，保存全部更改。

## <a name="next-steps"></a>后续步骤
  [深入了解](../virtual-network/virtual-network-network-interface-vm.md) Azure 虚拟机的网络接口。

<!-- Update_Description: update meta properties -->