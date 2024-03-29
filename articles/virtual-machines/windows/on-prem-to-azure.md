---
title: 从 AWS 和其他平台迁移到 Azure 中的托管磁盘 | Azure
description: 在 Azure 中使用从其他云（如 AWS 或其他虚拟化平台）上传的 VHD 并利用 Azure 托管磁盘创建 VM。
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
origin.date: 10/07/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 04d7963a45b3bf20d75998062a374d7639531dd8
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003966"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>从 Amazon Web Services (AWS) 和其他平台迁移到 Azure 中的托管磁盘

可将 VHD 文件从 AWS 或本地虚拟化解决方案上传到 Azure，以创建可利用托管磁盘的 VM。 Azure 托管磁盘不需要为 Azure IaaS VM 管理存储帐户。 必须仅指定所需类型（高级或标准）和磁盘大小，Azure 将创建和管理磁盘。 

可以上传通用和专用 VHD。 
- **通用 VHD** - 已使用 Sysprep 删除所有个人帐户信息。 
- “专用 VHD” - 维护来自原始 VM 的用户帐户、应用程序和其他状态数据。 

> [!IMPORTANT]
> 将任何 VHD 上传到 Azure 之前，应该遵循[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
>
>

| 方案                                                                                                                         | 文档                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 拥有要使用托管磁盘迁移至 Azure VM 的现有 AWS EC2 实例。                              | [将 VM 从 Amazon Web Services (AWS) 移到 Azure](aws-to-azure.md)                           |
| 拥有要用作映像的来自其他虚拟化平台的 VM，以创建多个 Azure VM。 | [上传通用 VHD 并用其在 Azure 中新建 VM](upload-generalized-managed.md) |
| 有一个唯一自定义 VM，你想要在 Azure 中重新创建它。                                                      | [将专用 VHD 上传到 Azure 并创建新 VM](create-vm-specialized.md)         |

## <a name="overview-of-managed-disks"></a>托管磁盘概述

Azure 托管磁盘无需管理存储帐户，从而简化了 VM 管理。 托管磁盘还受益于可用性集中 VM 的更佳可靠性。 这可确保将可用性集中不同 VM 的磁盘最大限度地彼此独立，以避免单点故障。 它会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单元（戳），限制由于硬件和软件故障引起的单个存储缩放单元故障影响。
根据需求，可以从三种类型的存储选项中进行选择。 若要了解可用的磁盘类型，请参阅[选择磁盘类型](disks-types.md)一文。

<!--Not Available on Ulter SSD-->

## <a name="plan-for-the-migration-to-managed-disks"></a>计划迁移到托管磁盘

本部分可帮助你针对 VM 和磁盘类型做出最佳决策。

如果打算从非托管磁盘迁移到托管磁盘，则应注意具有[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色的用户不能更改 VM 大小（因为它们可以预转换）。 这是因为包含托管磁盘的 VM 要求用户对 OS 磁盘具有 Microsoft.Compute/disks/write 权限。

### <a name="location"></a>位置

选取 Azure 托管磁盘可用位置。 如果要迁移到高级托管磁盘，还应确保高级存储在计划迁移到的区域中可用。 有关可用位置的最新信息，请参阅 [Azure 服务（按区域）](https://www.azure.cn/support/service-dashboard/#services) 。

### <a name="vm-sizes"></a>VM 大小

如果要迁移到高级托管磁盘，需要将 VM 的大小更新为该 VM 所在区域中支持高级存储的可用大小。 查看支持高级存储的 VM 大小。 [虚拟机大小](sizes.md)中列出了 Azure VM 大小规范。
查看适用于高级存储的虚拟机的性能特征并选择最适合工作负荷的 VM 大小。 确保 VM 上有足够的带宽来驱动磁盘通信。

### <a name="disk-sizes"></a>磁盘大小

**高级托管磁盘**

有 7 种类型的高级托管磁盘可用于 VM，每种磁盘都具有特定的 IOPS 和吞吐量限制。 根据应用程序在容量、性能、可伸缩性和峰值负载方面的需要为 VM 选择高级磁盘类型时，需要考虑这些限制。

| 高级磁盘类型  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 磁盘大小           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| 每个磁盘的 IOPS       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| 每个磁盘的吞吐量 | 每秒 25 MB  | 每秒 50 MB  | 每秒 100 MB | 每秒 125 MB |每秒 150 MB | 每秒 200 MB | 每秒 250 MB | 每秒 250 MB |

**标准托管磁盘**

有 7 种类型的标准托管磁盘可用于 VM。 其中每种磁盘都具有不同的容量，但具有相同的 IOPS 和吞吐量限制。 根据应用程序的容量要求，选择标准托管磁盘的类型。

| 标准磁盘类型  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| 磁盘大小           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| 每个磁盘的 IOPS       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| 每个磁盘的吞吐量 | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB |每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 

### <a name="disk-caching-policy"></a>磁盘缓存策略 

**高级托管磁盘**

默认情况下，所有高级数据磁盘的磁盘缓存策略都是“只读”，所有附加到 VM 的高级操作系统都是“读写”。 为使应用程序的 IO 达到最佳性能，建议使用此配置设置。 对于频繁写入或只写的磁盘（例如 SQL Server 日志文件），禁用磁盘缓存可获得更佳的应用程序性能。

### <a name="pricing"></a>定价

查看[托管磁盘定价](https://www.azure.cn/pricing/details/storage/)。 高级托管磁盘的定价与高级非托管磁盘相同。 但标准托管磁盘的定价与标准非托管磁盘不同。

## <a name="next-steps"></a>后续步骤

- 将任何 VHD 上传到 Azure 之前，应该遵循[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)

<!--Update_Description: update meta properties, wording update -->