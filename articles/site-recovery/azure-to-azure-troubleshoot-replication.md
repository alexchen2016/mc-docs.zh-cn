---
title: 针对持续存在的 Azure 到 Azure 复制问题进行 Azure Site Recovery 故障排除 | Azure
description: 排查复制 Azure 虚拟机进行灾难恢复时出现的错误和问题
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: troubleshooting
origin.date: 11/27/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: a41080e341fb8256e155f049b7a652b4d92ea3f0
ms.sourcegitcommit: f1ecc209500946d4f185ed0d748615d14d4152a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463532"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>排查 Azure 到 Azure VM 复制中持续出现的问题

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机时在 Azure Site Recovery 中经常出现的问题。 另外还介绍了如何排查这些问题。 有关支持的配置的详细信息，请参阅 [support matrix for replicating Azure VMs](site-recovery-support-matrix-azure-to-azure.md)（复制 Azure VM 的支持矩阵）。

Azure Site Recovery 以一致的方式将数据从源区域复制到灾难恢复区域，并每隔 5 分钟创建崩溃一致性恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

错误消息：“在过去 60 分钟内没有可供 VM 使用的崩溃一致性恢复点。”</br>
错误 ID：153007 </br>

以下部分介绍原因和解决方案。

<a name="high-data-change-rate-on-the-source-virtal-machine"></a>
## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>源虚拟机上的数据更改率较高
如果源虚拟机上的数据更改率超过支持的限制，Azure Site Recovery 将激发事件。 若要检查问题是否由高变动率造成，请转到“复制的项” > “VM” > “事件 - 过去 72 小时”。
你应当会看到事件“数据更改率超过支持的限制”：

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果选择该事件，应会看到确切的磁盘信息：

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序 I/O 组合。 实际结果可能因应用程序 I/O 组合而异。 

有两个限制需要考虑：每个磁盘的数据变动率，以及每个虚拟机的数据变动率。 有关示例，请看下表中的高级 P20 磁盘。 Site Recovery 能够处理每个磁盘 5 MB/秒的变动率，但由于每个 VM 的总变动率限制为 25 MB/秒，因此，它最多可以处理每个 VM 中 5 个这样的磁盘。

**复限制复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |10 MB/秒 | 每个磁盘 842 GB

### <a name="solution"></a>解决方案
Azure Site Recovery 根据磁盘类型实施数据更改率限制。 若要判断此问题是重复性的还是暂时性的，请确定受影响虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：

![确定数据更改率的三步过程](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。
2. 监视屏幕截图中所示的峰值。
3. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

如果峰值是由于偶尔出现的数据迸发，数据更改率间或高于 10 MB/秒（针对高级存储）和 2 MB/秒（针对标准存储），但随后又降下来，则复制可同步。 但是，如果变动率在大多数时间远远超过支持的限制，则在可能情况下，请考虑以下选项之一：

* **排除导致数据更改率变高的磁盘**：可以使用 [PowerShell](/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) 来排除磁盘。
* **更改灾难恢复存储磁盘层**：仅当磁盘数据变动率小于 10 MB/秒时，才可以使用此选项。 假设包含 P10 磁盘的 VM 的数据变动率大于 8 MB/秒，但小于 10 MB/秒。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。

<a name="Network-connectivity-problem"></a>
## <a name="network-connectivity-problems"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟
Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将数据从虚拟机上传到缓存存储帐户时的速度低于 4 MB/3 秒，则可能会出现网络延迟。 

若要检查是否存在与延迟相关的问题，请使用 [azcopy](/storage/common/storage-use-azcopy) 将数据从虚拟机上传到缓存存储帐户。 如果延迟较高，请检查你是否在使用网络虚拟设备 (NVA) 控制来自 VM 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。 

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接
要使 Site Recovery 复制正常运行，必须具有从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。 若要确保所有 URL 都已连接，请参阅 [Site Recovery URL 的出站连接](/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。

<!-- Update_Description: update meta properties, wording update -->