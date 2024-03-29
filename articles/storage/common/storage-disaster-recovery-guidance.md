---
title: 灾难恢复和存储帐户故障转移（预览版）- Azure 存储
description: Azure 存储支持异地冗余存储帐户故障转移（预览版）。 通过帐户故障转移，可以在主终结点不可用时为存储帐户启动故障转移过程。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 02/01/2019
ms.date: 02/25/2019
ms.author: v-jay
ms.subservice: common
ms.openlocfilehash: 28b3d883477a6328ad988b8ac22c85c51c1076de
ms.sourcegitcommit: 0fd74557936098811166d0e9148e66b350e5b5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56665420"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Azure 存储中的灾难恢复和存储帐户故障转移（预览版）

Azure 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 如果应用程序需要复原能力，Azure 建议使用异地冗余存储，这样就可以将数据复制到另一个区域。 此外，客户还应制定用于处理区域服务中断的灾难恢复计划。 灾难恢复计划的一个重要组成部分是，准备在主终结点不可用时将故障转移到辅助终结点。 

## <a name="choose-the-right-redundancy-option"></a>选择正确的冗余选项

为了实现冗余，所有存储帐户都会进行复制。 为帐户选择哪个冗余选项取决于所需的复原能力水平。 若要防止区域中断，请选择有权或无权从次要区域读取数据的异地冗余存储：  

**异地冗余存储 (GRS)**：在至少相距数百英里的两个地理区域之间异步复制数据。 如果主要区域遭遇服务中断，次要区域便会成为数据的冗余源。 可以通过启动故障转移，将辅助终结点转换为主终结点。

**读取访问权限异地冗余存储 (RA-GRS)**：为异地冗余存储提供附加优势，即对辅助终结点的读取访问权限。 如果主终结点发生中断，配置了 RA-GRS 且旨在实现高可用性的应用程序可以继续从辅助终结点读取数据。 我们建议对应用程序使用 RA-GRS，以获取最大复原能力。

> [!WARNING]
> 异地冗余存储有数据丢失风险。 数据是异步复制到次要区域。也就是说，数据写入主要区域与数据写入次要区域之间存在延迟。 在服务中断的情况下，对主终结点执行、但尚未复制到辅助终结点的写入操作将会丢失。 

## <a name="design-for-high-availability"></a>旨在实现高可用性

请务必从一开始就设计高可用性应用程序。 有关设计应用程序和计划灾难恢复方面的指导，请参阅以下 Azure 资源：

* [设计使用 RA-GRS 的高可用性应用程序](storage-designing-ha-apps-with-ragrs.md)：有关生成利用 RA-GRS 的应用程序的设计指南。
* [教程：生成使用 Blob 存储的高可用性应用程序](../blobs/storage-create-geo-redundant-storage.md)：介绍了如何生成在模拟故障和恢复时自动切换终结点的高可用性应用程序的教程。 

此外，还请注意下面这些可保持 Azure 存储数据高可用性的最佳做法：

* **磁盘：** 利用 [Azure 备份](/backup/)备份 Azure 虚拟机使用的 VM 磁盘。 还建议在发生区域灾难时使用 [Azure Site Recovery](/site-recovery/) 保护 VM。
* **块 blob：** 启用[软删除](../blobs/storage-blob-soft-delete.md)以防发生对象级删除和覆盖，或使用 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md) 或 [Azure 数据移动库](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)将块 blob 复制到其他区域中的另一个存储帐户内。
* **文件：** 使用 [AzCopy](storage-use-azcopy.md) 或 [Azure PowerShell](storage-powershell-guide-full.md) 将文件复制到其他区域中的另一个存储帐户内。
* **表：** 使用 [AzCopy](storage-use-azcopy.md) 将表数据导出到其他区域中的另一个存储帐户内。

## <a name="track-outages"></a>跟踪服务中断

客户可以订阅 [Azure 服务运行状况仪表板](https://www.azure.cn/support/service-dashboard/)，以跟踪 Azure 存储和其他 Azure 服务的运行状况和状态。

Azure 还建议将应用程序设计为可以应对可能出现的写入故障。 应用程序应公开写入故障，以提醒你主要区域可能存在服务中断。

## <a name="azure-managed-failover"></a>Azure 托管的故障转移

在由于重大灾难而导致区域丢失的极端情况下，Azure 可能会启动区域故障转移。 在此情况下，不需要采取任何操作。 在 Azure 托管的故障转移完成之前，你对存储帐户不拥有写入访问权限。 如果存储帐户已配置 RA-GRS，应用程序可以从次要区域读取数据。 

## <a name="see-also"></a>另请参阅

* [使用 RA-GRS 设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)
* [教程：生成使用 Blob 存储的高可用性应用程序](../blobs/storage-create-geo-redundant-storage.md) 
