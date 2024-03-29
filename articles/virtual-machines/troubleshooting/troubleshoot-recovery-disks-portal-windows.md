---
title: 在 Azure 门户中使用 Windows 故障排除 VM | Azure
description: 了解如何通过使用 Azure 门户将 OS 磁盘连接到恢复 VM，对 Azure 中的 Windows 虚拟机问题进行故障排除
services: virtual-machines-windows
documentationCenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 08/13/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 6e1fd71a34be9d0528d502f7874fed513fe89be4
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003954"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>通过使用 Azure 门户将 OS 磁盘附加到恢复 VM，对 Windows VM 进行故障排除
如果 Windows 虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure 门户将虚拟硬盘连接到另一个 Windows VM 来修复所有错误，然后重新创建原始 VM。

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 删除遇到问题的 VM，保留虚拟硬盘。
2. 将虚拟硬盘附加并装入到另一个 Windows VM，以便进行故障排除。
3. 连接到故障排除 VM。 编辑文件或运行任何工具以修复原始虚拟硬盘上的问题。
4. 从故障排除 VM 卸载并分离虚拟硬盘。
5. 使用原始虚拟硬盘创建 VM。

对于使用托管磁盘的 VM，现在可以使用 Azure PowerShell 更改 VM 的 OS 磁盘， 而不再需要删除并重新创建 VM。 有关详细信息，请参阅[通过使用 Azure PowerShell 将 OS 磁盘附加到恢复 VM 来对 Windows VM 进行故障排除](troubleshoot-recovery-disks-windows.md)。

## <a name="determine-boot-issues"></a>确定启动问题
若要确定 VM 不能正常启动的原因，请检查启动诊断 VM 屏幕截图。 一个常见的例子是应用程序更新失败，或底层虚拟硬盘已删除或移动。

在门户中选择 VM，然后向下滚动到“支持 + 故障排除”部分。  单击“启动诊断”查看屏幕快照。 记下任何特定的错误消息或错误代码，帮助确定 VM 遇到问题的原因。 

![查看 VM 启动诊断控制台日志](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

也可单击“下载屏幕截图”，下载捕获的 VM 屏幕截图。

## <a name="view-existing-virtual-hard-disk-details"></a>查看现有虚拟硬盘的详细信息
在将虚拟硬盘附加到另一个 VM 之前，需要标识虚拟硬盘 (VHD) 的名称。 

在门户中选择资源组，并选择存储帐户。 单击“Blob”，如以下示例中所示： 

![选择存储 Blob](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

通常，虚拟硬盘存储在一个名为 **vhds** 的容器中。 请选择该容器查看虚拟硬盘的列表。 记下 VHD 的名称（前缀通常是 VM 的名称）：

![标识存储容器中的 VHD](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

从列表中选择现有的虚拟硬盘，并复制 URL 供后续步骤使用：

![复制现有虚拟硬盘的 URL](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)

## <a name="delete-existing-vm"></a>删除现有 VM
虚拟硬盘和 VM 在 Azure 中是两个不同的资源。 虚拟硬盘是操作系统本身，存储应用程序和配置。 VM 本身只是定义大小或位置的元数据，引用虚拟硬盘或虚拟网络接口卡 (NIC) 等资源。 每个虚拟硬盘在附加到 VM 时分配有一个租约。 尽管 VM 正在运行时也可以附加和分离数据磁盘，但是，若要分离 OS 磁盘，则必须删除 VM 资源。 即使 VM 处于停止和解除分配状态，租约也继续将 OS 磁盘与 VM 相关联。

恢复 VM 的第一步是删除 VM 资源本身。 删除 VM 时会将虚拟硬盘留在存储帐户中。 删除 VM 后，可将虚拟硬盘附加到另一个 VM，以进行故障排除和解决这些错误。

在门户中选择用户的 VM，并单击“删除”： 

![显示启动错误的 VM 启动诊断屏幕截图](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

等到 VM 已完成删除，再将虚拟硬盘附加到另一个 VM。 虚拟硬盘上将其与 VM 关联的租约需要释放，才能将虚拟硬盘附加到另一个 VM。

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>将现有虚拟硬盘附加到另一个 VM
在后续几个步骤中，使用另一个 VM 进行故障排除。 将现有虚拟硬盘附加到此故障排除 VM，以便浏览和编辑磁盘的内容。 例如，此过程允许用户更正任何配置错误或者查看其他应用程序或系统日志文件。 选择或创建另一个 VM 用于故障排除。

1. 在门户中选择资源组，并选择故障排除 VM。 选择“磁盘”，然后单击“附加现有磁盘”：

    ![在门户中附加现有磁盘](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 若要选择现有的虚拟硬盘，请单击“VHD 文件”： 

    ![浏览现有 VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. 选择存储帐户和容器，并单击现有的 VHD。 单击“选择”按钮确认所做的选择： 

    ![选择现有 VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. 选择 VHD 后，请单击“确定”附加现有虚拟硬盘： 

    ![确认附加现有虚拟硬盘](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. 几秒钟后，VM 的“磁盘”窗格列出作为数据磁盘连接的现有虚拟硬盘： 

    ![现有虚拟硬盘已附加为数据磁盘](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)

## <a name="mount-the-attached-data-disk"></a>装载附加的数据磁盘

1. 打开到 VM 的远程桌面连接。 在门户中选择 VM，然后单击“连接”。 下载并打开 RDP 连接文件。 输入登录 VM 所需的凭据，如下所示：

    ![使用远程桌面登录到 VM](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. 打开“服务器管理器”，然后选择“文件和存储服务”。 

    ![在“服务器管理器”中选择“文件和存储服务”](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 系统会自动检测并附加数据磁盘。 若要查看已连接磁盘的列表，请选择“磁盘”。 可选择要查看卷信息（包括驱动器号）的数据磁盘。 以下示例显示了使用 **F:** 的附加数据磁盘：

    ![服务器管理器中的附加磁盘和卷信息](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决错误后，可从故障排除 VM 中分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘用于任何其他 VM。

1. 在与 VM 的 RDP 会话中，打开“服务器管理器”，然后选择“文件和存储服务”：

    ![在服务器管理器中选择“文件和存储服务”](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. 选择“磁盘”  ，并选择数据磁盘。 右键单击数据磁盘，并选择“脱机” ：

    ![在服务器管理器中将数据磁盘设置为脱机](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 现在从 VM 中分离虚拟硬盘。 在 Azure 门户中选择 VM，然后单击“磁盘”。 Select your existing virtual hard disk and then click <bpt id="p1">**</bpt>Detach<ept id="p1">**</ept>:

    ![分离现有虚拟硬盘](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    等到 VM 成功分离数据磁盘，并继续操作。

## <a name="create-vm-from-original-hard-disk"></a>从原始硬盘创建 VM
若要从原始虚拟硬盘创建 VM，请使用 [此 Azure Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet)。 该模板使用前面命令中的 VHD URL 将 VM 部署到现有的或新的虚拟网络。 单击“部署到 Azure”按钮，如下所示： 

[![D部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F201-vm-specialized-vhd-new-or-existing-vnet%2Fazuredeploy.json)

![从 GitHub 中的模板部署 VM](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)


模板已载入 Azure 门户进行部署。 请输入新 VM 和现有 Azure 资源的名称，并粘贴现有虚拟硬盘的 URL。 若要开始部署，请单击“购买”： 

![从模板部署 VM](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

## <a name="re-enable-boot-diagnostics"></a>重新启用启动诊断
从现有虚拟硬盘创建 VM 时，启动诊断可能不会自动启用。 要检查启动诊断的状态并根据需要打开启动诊断，请在门户中选择 VM。 在“监视”下面，单击“诊断设置”。 确保状态为“打开”，并检查“启动诊断”旁边的复选标记是否为选中状态。 If you make any changes, click <bpt id="p1">**</bpt>Save<ept id="p1">**</ept>:

![更新启动诊断设置](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[对 Azure VM 的 RDP 连接进行故障排除](troubleshoot-rdp-connection.md)。 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[对 Windows VM 上的应用程序连接问题进行故障排除](troubleshoot-app-connection.md)。

有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。

<!--Update_Description: update meta properties, wording update, update link -->
