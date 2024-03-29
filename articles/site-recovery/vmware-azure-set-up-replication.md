---
title: 使用 Azure Site Recovery 配置和管理用于从 VMware 灾难恢复到 Azure 的复制策略 | Azure
description: 介绍如何配置复制设置，以便使用 Azure Site Recovery 从 VMware 灾难恢复到 Azure。
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: conceptual
origin.date: 11/27/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: a0ee1eb32d64c23869be765d798b2039bbe4d533
ms.sourcegitcommit: 26957f1f0cd708f4c9e6f18890861c44eb3f8adf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363323"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>配置和管理用于从 VMware 灾难恢复到 Azure 的复制策略
本文介绍如何配置复制策略，以便使用 [Azure Site Recovery](site-recovery-overview.md) 将 VMware VM 复制到 Azure。

## <a name="create-a-policy"></a>创建策略

1. 选择“管理” > “Site Recovery 基础结构”。
1. 在“适用于 VMware 和物理计算机”中选择“复制策略”。 
1. 单击“+复制策略”，并指定策略名称。
1. 在“RPO 阈值”中：指定 RPO 限制。 当连续复制超出此限制时，将生成警报。
1. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个保留期时段内的任意时间点。 复制到高级存储的计算机最长支持 24 小时的保留期。 标准存储最长支持 72 个小时。
1. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。
1. 单击 **“确定”**。 应会在 30 秒到 1 分钟之内创建好策略。

创建复制策略时，会使用后缀“failback”自动创建相应的故障回复复制策略。 创建策略后，可以通过选择它 >“编辑设置”对其进行编辑。

## <a name="associate-a-configuration-server"></a>关联配置服务器 

将复制策略与本地配置服务器相关联。

1. 单击“关联”，并选择配置服务器。

    ![关联配置服务器](./media/vmware-azure-set-up-replication/associate1.png)

1. 单击 **“确定”**。 应会在 1 到 2 分钟内关联配置服务器。

    ![配置服务器关联](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="disassociate-or-delete-a-replication-policy"></a>取消关联或删除复制策略
1. 选择复制策略。
    a. 若要将策略与配置服务器取消关联，请确保没有复制计算机在使用该策略。 然后，单击“取消关联”。
    b. 若要删除策略，请确保它未与配置服务器关联。 然后单击“删除”。 应需要 30-60 秒才能删除。
1. 单击 **“确定”**。

<!-- Update_Description: update meta properties -->