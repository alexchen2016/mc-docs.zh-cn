---
title: 在 Azure 磁盘存储上对应用程序进行基准测试 - 托管磁盘
description: 了解在 Azure 上对应用程序进行基准测试的过程。
services: virtual-machines-windows,storage
author: rockboyfor
ms.author: v-yeche
origin.date: 01/11/2019
ms.date: 04/01/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 06051fae634d5f48091d4b498636b13e674f7ea8
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004387"
---
# <a name="benchmarking-a-disk"></a>磁盘基准测试

基准测试是指模拟应用程序的不同工作负荷，针对每个工作负荷来测量应用程序性能这样一个过程。 通过[为实现高性能而设计](premium-storage-performance.md)一文中描述的步骤，你已经收集了应用程序性能要求。 通过在托管应用程序的 VM 上运行基准测试工具，可以确定应用程序在高级存储中能够达到的性能级别。 在本文中，我们提供了如何对预配了 Azure 高级存储磁盘的标准 DS14 VM 进行基准测试的示例。

我们使用了常见的基准测试工具 Iometer 和 FIO，分别适用于 Windows 和 Linux。 这些工具会生成多个线程，这些线程模拟类似生产的工作负荷，并测量系统性能。 使用这些工具还可以配置各种参数（例如块大小和队列深度），应用程序的这些参数通常无法更改。 这样，就可以在预配了高级磁盘的大规模 VM 上，为不同类型的应用程序工作负荷灵活实现最高性能。 若要详细了解每种基准测试工具，请参阅 [Iometer](http://www.iometer.org/) 和 [FIO](http://freecode.com/projects/fio)。

如果要按以下示例进行操作，请创建一个标准 DS14 VM，并将 11 个高级存储磁盘连接到 VM。 在这 11 个磁盘中，将 10 个磁盘的主机缓存配置为“无”，并将它们条带化到名为 NoCacheWrites 的卷中。 将剩余磁盘上的主机缓存配置为“ReadOnly”，在该磁盘上创建名为 CacheReads 的卷。 进行这样的设置以后，便可以看到标准 DS14 VM 展现出最大的读写性能。 有关创建具有高级 SSD 的 DS14 VM 的详细步骤，请转至[为实现高性能而设计](premium-storage-performance.md)。

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>后续步骤

继续按照“为实现高性能而设计”一文进行操作。 其中，你要创建一个类似于现有原型应用程序的清单。 使用各种能够用来模拟工作负荷并衡量原型应用程序性能的基准测试工具。 这样做可以确定哪些磁盘产品可以满足或超过你的应用程序性能要求。 然后可为生产应用程序实施相同准则。

> [!div class="nextstepaction"]
> 请参阅[为实现高性能而设计](premium-storage-performance.md)一文。