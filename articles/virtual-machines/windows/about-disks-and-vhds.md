---
title: 关于 Azure Windows VM 的非托管（页 blob）和托管磁盘存储 | Azure
description: 了解有关 Azure 中的 Windows 虚拟机的非托管（页 blob）和托管磁盘存储的基础知识。
services: virtual-machines-windows,storage
author: rockboyfor
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.topic: article
origin.date: 11/15/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.subservice: disks
ms.openlocfilehash: 780e8a66f7a7c8a4adf80e70864376abd319f6cc
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56666096"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>关于 Azure Windows VM 的磁盘存储

就像其他任何计算机一样，Azure 中的虚拟机将磁盘用作存储操作系统、应用程序和数据的位置。 所有 Azure 虚拟机都至少有两个磁盘，即 Windows 操作系统磁盘和临时磁盘。 操作系统磁盘基于映像创建，操作系统磁盘和该映像都存储在 Azure 存储帐户中的虚拟硬盘 (VHD) 内。 虚拟机还可以有一个或多个数据磁盘，而这些磁盘也存储为 VHD。 

在本文中，我们将讨论磁盘的不同用法，并讨论可以创建和使用的不同磁盘类型。 本文也适用于 [Linux 虚拟机](../linux/about-disks-and-vhds.md)。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM 使用的磁盘

让我们来看看 VM 如何使用磁盘。

### <a name="operating-system-disk"></a>操作系统磁盘

每个虚拟机都附加了一个操作系统磁盘。 默认情况下，它注册为 SATA 驱动器并标为 C: 盘。 此磁盘的最大容量为 2048 GB。

### <a name="temporary-disk"></a>临时磁盘

每个 VM 包含一个临时磁盘。 临时磁盘为应用程序和进程提供短期存储存储空间，仅用于存储页面或交换文件等数据。 在[维护事件](manage-availability.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期间或[重新部署 VM](redeploy-to-new-node.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json) 时，临时磁盘上的数据可能会丢失。 在 VM 成功标准重启期间，临时驱动器上的数据应保留。 但是，有些情况下数据可能不会持久保留，例如移到新主机。 因此，临时驱动器上的任何数据都不应该是对系统至关重要的数据。 在设计使用临时驱动器作为数据缓存来提高应用程序性能的应用程序时，应在设计中假定临时驱动器上的数据缓存会在重启期间丢失，且应用程序需要时间来重新生成数据缓存，然后才能达到类似的性能。

临时磁盘默认标记为 D: 盘，用于存储 pagefile.sys。 若要将此磁盘重新映射到其他驱动器号，请参阅[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md)。 临时磁盘的大小因虚拟机的大小而异。 有关详细信息，请参阅 [Windows 虚拟机的大小](sizes.md)。

有关 Azure 如何使用临时磁盘的详细信息，请参阅 [Understanding the temporary drive on Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

### <a name="data-disk"></a>数据磁盘

数据磁盘是附加到虚拟机的 VHD，用于存储应用程序数据或其他需要保留的数据。 数据磁盘注册为 SCSI 驱动器并且带有所选择的字母标记。 每个数据磁盘的最大容量为 4,095 GB，托管磁盘的最大容量为 4,095 GiB。 虚拟机的大小决定了可附加的磁盘数目，以及可用来托管磁盘的存储类型。

<!-- Max Capacity is 4,095 GiB NOT 32,767 GiB-->

> [!NOTE]
> 有关虚拟机容量的详细信息，请参阅 [Windows 虚拟机的大小](sizes.md)。

当你基于映像创建虚拟机时，Azure 会创建操作系统磁盘。 如果使用包含数据磁盘的映像，则 Azure 还会在创建虚拟机时创建数据磁盘。 否则，需要在创建虚拟机后添加数据磁盘。

随时可以将数据磁盘添加到虚拟机，只需将该磁盘**附加** 到虚拟机即可。 可以使用已上传或复制到存储帐户的 VHD，也可以使用 Azure 为你创建的空 VHD。 附加数据磁盘会将 VHD 文件与 VM 关联，方法是在 VHD 上放置“租约”，因此在仍附加 VHD 时无法从存储中删除它。

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]
<!-- Not Available on For preview sizes, see our [FAQ](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged)-->

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>最后一个建议：结合使用 TRIM 与非托管标准磁盘

如果使用非托管标准磁盘 (HDD)，则应启用 TRIM。 TRIM 会放弃磁盘上未使用的块，以便仅对实际使用的存储进行收费。 如果创建了较大的文件，并将其删除，这样可以节省成本。

可以运行此命令来检查 TRIM 设置。 在 Windows VM 上打开命令提示符，并键入：

```
fsutil behavior query DisableDeleteNotify
```

如果该命令返回 0，则表示正确启用了 TRIM。 如果返回 1，请运行以下命令启用 TRIM：

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> 注意：TRIM 支持自 Windows Server 2012/Windows 8 及更高版本起开始提供。请参阅[新 API 允许应用向存储媒体发送“TRIM 和 Unmap”提示](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints)。
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>后续步骤
* [附加磁盘](attach-disk-portal.md)可为 VM 添加额外的存储。
* [创建快照](snapshot-copy-managed-disk.md)。
* [转换为托管磁盘](convert-unmanaged-to-managed-disks.md)。

<!--Update_Description: update meta properties, wording update -->