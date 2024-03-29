---
title: 使用 Azure 备份服务管理和监视 Azure VM 备份
description: 了解如何使用 Azure 备份服务管理和监视 Azure VM 备份。
services: backup
author: lingliw
manager: digimobile
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: v-lingwu
ms.openlocfilehash: 79f5005a87617e4ec478d61f5d3d49336875c3e2
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555448"
---
# <a name="manage-azure-vm-backups"></a>管理 Azure VM 备份

本文介绍了如何使用 [Azure 备份服务](backup-overview.md)管理 Azure 虚拟机 (VM)。 本文还概述了可以在保管库仪表板上找到的备份信息。


在 Azure 门户中，可以使用恢复服务保管库仪表板访问保管库信息，包括：

* 最新备份，也是最新还原点。
* 备份策略。
* 所有备份快照的总大小。
* 启用了备份的 VM 数。

可以使用仪表板以及通过向下钻取到各个 VM 来管理备份。 若要开始计算机备份，请在仪表板上打开保管库。

![具有滑块的完整仪表板视图](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>在仪表板中查看 VM

若要在仪表板中查看 VM，请执行以下操作：
1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 在“中心”菜单上选择“浏览”。 在资源列表中，键入“恢复服务”。 在键入时，列表会根据你的输入进行筛选。 选择“恢复服务保管库”。

    ![创建恢复服务保管库](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 为了便于使用，请右键单击保管库，然后选择“固定到仪表板”。
4. 打开保管库仪表板。

    ![打开保管库仪表板和“设置”边栏选项卡](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 在“备份项”磁贴中，选择“Azure 虚拟机”。

    ![打开“备份项”磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 在“备份项”边栏选项卡上，可以查看受保护的 VM 的列表。 在此示例中，保管库保护着一台虚拟机：demobackup。  

    ![查看“备份项”边栏选项卡](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 从保管库项的仪表板中，修改备份策略，运行按需备份，停止或恢复 VM 保护，删除备份数据，查看还原点，以及运行还原。

    ![“备份项”仪表板和“设置”边栏选项卡](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>管理 VM 的备份策略

若要管理备份策略，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.cn/)。 打开保管库仪表板。
2. 在“备份项”磁贴中，选择“Azure 虚拟机”。

    ![打开“备份项”磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 在“备份项”边栏选项卡上，可以查看受保护的 VM 的列表，以及最后的备份状态和最新还原点时间。

    ![查看“备份项”边栏选项卡](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 从保管库项的仪表板中，你可以选择备份策略。

   * 若要切换策略，请选择其他策略，然后选择“保存”。 新策略立即应用于保管库。

     ![选择备份策略](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>运行按需备份
为 VM 设置保护后，可以为 VM 运行按需备份。 请记住以下详细信息：

- 如果初始备份已挂起，则按需备份会在恢复服务保管库中创建 VM 的完整副本。
- 如果初始备份已完成，按需备份仅将以前快照的更改发送到恢复服务保管库。 也就是说，后续备份始终是增量备份。
- 按需备份的保留期范围是你在触发备份时指定的保留期值。

若要触发按需备份，请执行以下操作：

1. 在[保管库项仪表板](#view-vms-on-the-dashboard)上，在“受保护的项”下，选择“备份项”。

    ![“立即备份”选项](./media/backup-azure-manage-vms/backup-now-button.png)

2. 从“备份管理类型”下，选择“Azure 虚拟机”。 “备份项(Azure 虚拟机)”边栏选项卡随即出现。
3. 选择一个 VM，选择“立即备份”来创建一个按需备份。 “立即备份”边栏选项卡随即出现。
4. 在“备份保留截止日期”字段中，指定要将备份保留到的日期。

    ![“立即备份”日历](./media/backup-azure-manage-vms/backup-now-check.png)

5. 选择“确定”以运行备份作业。

若要跟踪作业进度，请在保管库仪表板中，选择“备份作业”磁贴。

## <a name="stop-protecting-a-vm"></a>停止保护 VM

可通过两种方法来停止保护 VM：

- 停止所有将来的备份作业并删除所有恢复点。 在这种情况下将无法还原 VM。
- 停止所有将来的备份作业，并保留恢复点。 虽然将恢复点保留在保管库中需要付款，但你将能够根据需要还原 VM。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。

>[!NOTE]
>如果在不停止备份的情况下删除数据源，则新备份将会失败。 旧恢复点将根据策略过期，但始终会保留一个最后的恢复点，直至你显式停止备份并删除数据。
>

若要停止保护 VM，请执行以下操作：

1. 在[保管库项的仪表板](#view-vms-on-the-dashboard)上，选择“停止备份”。
2. 选择是保留还是删除备份数据，并根据需要确认你的选择。 如果需要，添加注释。 如果不确定项名称，将鼠标悬停在感叹号上面即可查看该名称。

    ![停止保护](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     一条通知将让你获悉备份作业已停止。

## <a name="resume-protection-of-a-vm"></a>恢复对 VM 的保护

如果你在停止 VM 时保留了备份数据，则以后可以恢复保护。 如果删除了备份数据，则无法恢复保护。

若要恢复 VM 保护，请执行以下操作：

1. 在[保管库项的仪表板](#view-vms-on-the-dashboard)上，选择“恢复备份”。

2. 按[管理备份策略](#manage-backup-policy-for-a-vm)中的步骤为 VM 分配策略。 不需要选择 VM 的初始保护策略。
3. 将备份策略应用于 VM 后，你会看到以下消息：

    ![指明已成功保护 VM 的消息](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>删除备份数据

你可以在“停止备份”作业期间或者在备份作业完成后删除 VM 的备份数据。 在删除备份数据之前，请记住以下详细信息：

- 在删除恢复点之前等待数天或数周可能是一个不错的主意。
- 与还原恢复点的过程不同，在删除备份数据时，不能选择要删除的具体恢复点。 如果删除备份数据，则会删除所有关联的恢复点。

停止或禁用 VM 的备份作业后，可以删除备份数据：


1. 在[保管库项仪表板](#view-vms-on-the-dashboard)中，选择“删除备份数据”。

    ![选择“删除备份”](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. 键入备份项的名称以确认你要删除恢复点。

    ![确认你要删除恢复点](./media/backup-azure-manage-vms/item-verification-box.png)

1. 若要删除项的备份数据，请选择“删除”。 一条通知消息将让你获悉备份数据已删除。

## <a name="next-steps"></a>后续步骤
- 了解如何[通过 VM 的设置备份 Azure VM](backup-azure-vms-first-look-arm.md)。
- 了解如何[还原 VM](backup-azure-arm-restore-vms.md)。
- 了解如何[监视 Azure VM 备份](backup-azure-monitor-vms.md)。
<!-- Update_Description: update metedata properties -->