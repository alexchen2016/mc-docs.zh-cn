---
title: 排查 Linux VM 经典部署问题 | Azure
description: 排查在 Azure 中新建 Linux 虚拟机时遇到的经典部署问题
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
origin.date: 09/06/2016
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: c585c888ab0bdf3c95ff047c39b70e11b3fd13b4
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674647"
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>排查在 Azure 中新建 Linux 虚拟机时遇到的经典部署问题
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 有关本文中的 Resource Manager 版本，请参阅[此处](../troubleshoot-deployment-new-vm.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集审核日志
若要开始故障排除，请收集审核日志，以识别与问题相关的错误。

在 Azure 门户中，单击“浏览” > “虚拟机” > “你的 Windows 虚拟机” > “设置” > “审核日志”。

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y：** 如果 OS 是通用的 Linux，并且是使用通用设置上传和/或捕获的，则不会有任何错误。 同理，如果 OS 是专用的 Linux，并且是使用专用设置上传和/或捕获的，也不会有任何错误。

**上传错误：**

**N<sup>1</sup>：** 如果 OS 是通用的 Linux，但是以专用设置上传的，则会发生预配超时错误，并且 VM 会卡在预配阶段。

**N<sup>2</sup>：** 如果 OS 是专用的 Linux，但是以通用设置上传的，则会发生预配失败错误，因为新 VM 是以原始计算机名称、用户名和密码运行的。

**解决方法：**

若要解决这两个错误，请上传原始 VHD、可用的本地设置、以及与该 OS（通用/专用）相同的设置。 若要以通用设置上传，请记得先运行 -deprovision。 有关详细信息，请参阅 [Create and Upload a Virtual Hard Disk that Contains the Linux Operating System](create-upload-vhd-classic.md)（创建并上传包含 Linux 操作系统的虚拟硬盘）。

**捕获错误：**

**N<sup>3</sup>：** 如果 OS 是通用的 Linux，但是以专用设置捕获的，则会发生预配超时错误，因为标记为通用的原始 VM 不可用。

**N<sup>4</sup>：** 如果 OS 是专用的 Linux，但是以通用设置捕获的，则会发生预配失败错误，因为新 VM 是以原始计算机名称、用户名和密码运行的。 此外，标记为专用的原始 VM 不可用。

**解决方法：**

若要解决这两个错误，请从门户中删除当前映像，并[从当前 VHD 重新捕获映像](capture-image-classic.md)，其设置与 OS 的设置相同（通用/专用）。

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>问题：自定义/库/市场映像；分配失败
当新的 VM 请求被发送到没有可用空间可处理请求、或不支持所请求的 VM 大小的群集，便发生此错误。 在相同的云服务中不可混合不同系列的 VM。 因此，如果想要创建和云服务可支持大小不同的新 VM，计算请求将失败。

可能遇到因两种情况造成的错误，取决于用于创建新 VM 的云服务的条件约束。

**原因 1：** 云服务已固定到特定群集，或者链接到地缘组，因而固定到所设计的特定群集。 因此，该地缘组中新的计算资源请求将于托管现有资源的相同群集中尝试发出。 但是，同一群集可能不支持请求的 VM 大小，或者可用空间不足，导致分配错误。 无论是通过新的云服务还是现有的云服务创建新资源，都是如此。

**解决方法 1：**

* 创建新的云服务，并将它与区域或基于区域的虚拟网络关联。
* 在新的云服务中创建新 VM。
  如果在尝试创建新的云服务时收到错误，请稍后再试一次，或更改云服务的区域。

> [!IMPORTANT]
> 如果尝试在现有的云服务中创建新的 VM，但无法创建，而又必须为新的 VM 创建新的云服务，则可以选择合并相同云服务中的所有 VM。 为此，请删除现有云服务中的 VM，并从它们位于新云服务中的磁盘重新撷取它们。 然而，请务必记住，新的云服务有新的名称和 VIP，因此需要为所有目前将此信息用于现有云服务的依赖项更新该信息。
> 
> 

**原因 2：** 云服务已经与链接到地缘组的虚拟网络关联，因而固定到所设计的特定群集。 该地缘组中的所有新计算资源请求将于托管现有资源的相同群集中尝试发出。 但是，同一群集可能不支持请求的 VM 大小，或者可用空间不足，导致分配错误。 无论是通过新的云服务还是现有的云服务创建新资源，都是如此。

**解决方法 2：**

* 创建新的区域虚拟网络。
* 在新的虚拟网络中创建新 VM。
* [将现有虚拟网络连接到](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)新虚拟网络。 详细了解 [区域虚拟网络](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。 此外，用户也可以 [将基于地缘组的虚拟网络迁移到区域虚拟网络](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)，并创建新 VM。

## <a name="next-steps"></a>后续步骤
如果你在 Azure 中启动已停止的 Linux VM 或调整现有 Linux VM 的大小时遇到问题，请参阅[排查在 Azure 中重新启动现有 Linux 虚拟机或调整其大小时遇到的经典部署问题](restart-resize-error-troubleshooting.md)。

<!--Update_Description: update meta properties -->