---
title: 从 Azure 复制到 Azure 的 Azure Site Recovery 支持矩阵 | Azure
description: 总结出于灾难恢复 (DR) 需求将 Azure 虚拟机 (VM) 从一个区域复制到另一个区域时 Azure Site Recovery 支持的操作系统和配置。
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.devlang: na
ms.topic: article
origin.date: 09/10/2018
ms.date: 11/19/2018
ms.author: v-yeche
ms.openlocfilehash: c9d9bd69f51da075f9a040d1101fc23d10a3eea5
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52655668"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>用于在 Azure 区域之间进行复制的支持矩阵

本文总结了使用 [Azure Site Recovery](site-recovery-overview.md) 服务在不同区域之间复制和恢复 Azure 虚拟机时支持的配置和组件。

## <a name="user-interface-options"></a>用户界面选项

用户界面 |  支持/不支持
--- | ---
**Azure 门户** | 支持
**PowerShell** | [使用 PowerShell 进行 Azure 到 Azure 的复制](azure-to-azure-powershell.md)
**REST API** | 目前不支持
**CLI** | 目前不支持

## <a name="resource-support"></a>资源支持

资源移动类型 | **详细信息**
--- | --- | ---
跨资源组移动保管库 | 不支持<br/><br/> 不能跨资源组移动恢复服务保管库。
**跨资源组移动计算/存储/网络资源** | 不支持。<br/><br/> 如果在复制后移动 VM 或相关组件（如存储/网络），则需要为 VM 禁用并重新启用复制。
**将 Azure VM 从一个订阅复制到另一个订阅以进行灾难恢复** | 在同一 Azure Active Directory 租户中受支持。 经典 VM 不支持。
**跨受支持的地理群集内的区域（订阅内和跨订阅）迁移 VM** | 在“资源管理器部署模型”VM 所在的同一 Azure Active Directory 租户中受支持。 “经典部署模型”VM 不支持。
**在同一区域内迁移 VM** | 不支持。

## <a name="support-for-replicated-machine-os-versions"></a>复制计算机 OS 版本支持

以下支持适用于在相应 OS 上运行的任何工作负荷。

#### <a name="windows"></a>Windows

- Windows Server 2016（服务器核心、带桌面体验的服务器）*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2（至少具有 SP1）

>[!NOTE]
>
> \* 不支持 Windows Server 2016 Nano Server。

#### <a name="linux"></a>Linux

<!-- Not Available - Red Hat Enterprise Linux 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 -->
- CentOS 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5
- Ubuntu 14.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7[（受支持的内核版本）](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8[（受支持的内核版本）](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1、SP2、SP3 [（受支持的内核版本）](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

<!-- Not Available - Oracle Enterprise Linux 6.4, 6.5 -->

（不支持复制计算机从 SLES 11 SP3 升级到 SLES 11 SP4。 如果已将复制计算机从 SLES 11SP3 升级到 SLES 11 SP4，则需要禁用复制，并在升级后再次对计算机启用保护。）

>[!NOTE]
>
> 使用基于密码的身份验证和登录的 Ubuntu 服务器如果使用 cloud-init 包配置云虚拟机，可能会在故障转移后禁用基于密码的登录（具体取决于 cloudinit 配置）。可以通过在 Azure 门户上已故障转移的虚拟机的“设置”菜单（在“支持 + 故障排除”部分下）上重置密码来在虚拟机上重新启用基于密码的登录。

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Ubuntu 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-generic 到 3.13.0-153-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic 到 3.13.0-151-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic 到 3.13.0-147-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic 到 3.13.0-144-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-generic 到 4.4.0-131-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-30-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-generic 到 4.4.0-128-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-generic 到 4.4.0-124-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-41-generic、<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-generic 到 4.4.0-119-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-38-generic、<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1012-azure |

### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Debian 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 到 3.2.0-5-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 到 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17、9.18 | 3.16.0-4-amd64 到 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 到 3.16.0-5-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 SUSE Linux Enterprise Server 12 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3） | 9.19 | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 到 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default 到 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3） | 9.18 | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 到 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default 到 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3） | 9.17 | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default 到 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>运行 Linux OS 的 Azure 虚拟机上支持的文件系统和来宾存储配置

* 文件系统：ext3、ext4、ReiserFS（仅限 Suse Linux Enterprise Server）和 XFS
* 卷管理器：LVM2
* 多路径软件：设备映射程序

## <a name="region-support"></a>区域支持

可在同一地理群集中的任意两个区域之间复制和恢复 VM。

地理群集 | **Azure 区域**
-- | --
中国 | 中国东部、中国东部 2、中国北部、中国北部 2

<!-- Available on China East 2 and China North 2 region on 11/14/2018 -->

## <a name="support-for-vmdisk-management"></a>VM/磁盘管理支持

**操作** | **详细信息**
-- | ---
调整复制的 VM 上的磁盘大小 | 支持
将磁盘添加到复制的 VM | 不支持。 需要为 VM 禁用复制，添加磁盘，然后再次启用复制。

## <a name="support-for-compute-configuration"></a>计算配置支持

**配置** | 支持/不支持 | **备注**
--- | --- | ---
大小 | 具有至少 2 个 CPU 内核和 1 GB RAM 的任意 Azure VM 大小 | 请参阅 [Azure 虚拟机大小](../virtual-machines/windows/sizes.md)
可用性集 | 支持 | 在门户中执行“启用复制”步骤时，如果使用默认选项，则会基于源区域配置自动创建可用性集。 随时都可在“复制项”>“设置”>“计算和网络”>“可用性集”中更改目标可用性集。
混合使用权益（HUB）VM | 支持 | 如果源 VM 启用了 HUB 许可证，则测试故障转移或故障转移 VM 也可使用 HUB 许可证。
虚拟机规模集 | 不支持 |
Azure 库映像 — 由 Azure 发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行就可受到支持
Azure 库映像 - 第三方发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行就可受到支持。
自定义映像 - 第三方发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行就可受到支持。
使用 Site Recovery 迁移 VM | 支持 | 如果使用 Site Recovery 将 VMware/物理计算机迁移到 Azure，需要先卸载较旧版本的移动服务并重启计算机，然后才可将其复制到另一个 Azure 区域。

## <a name="support-for-storage-configuration"></a>存储配置支持

**配置** | 支持/不支持 | **备注**
--- | --- | ---
最大 OS 磁盘大小 | 2048 GB | 请参阅 [VM 使用的磁盘。](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
最大数据磁盘大小 | 4095 GB | 请参阅 [VM 使用的磁盘。](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
数据磁盘数 | 特定 Azure VM 大小最多支持 64 个 | 请参阅 [Azure 虚拟机大小](../virtual-machines/windows/sizes.md)
临时磁盘 | 始终从复制中排除 | 复制时始终排除临时磁盘。 根据 Azure 指南，不应将任何永久性数据存储在临时磁盘中。 有关详细信息，请参阅 [Azure VM 上的临时磁盘](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk)。
磁盘上的数据更改速率 | 高级存储的每个磁盘上的数据更改率为 10 MBps，而标准存储的每个磁盘上的数据更改率为 2 MBps | 如果磁盘上的平均数据更改率连续超过 10 MBps（针对高级存储）和 2 MBps（针对标准存储），复制将不同步。 但是，如果只是偶尔出现数据迸发，数据更改率间或高于 10 MBps（针对高级存储）和 2 MBps（针对标准存储），但随后又降下来，则复制可同步。 在这种情况下，恢复点可能会稍有延迟。
标准存储帐户中的磁盘 | 支持 |
高级存储帐户中的磁盘 | 支持 | 如果 VM 的磁盘分散在高级和标准存储帐户中，可以为每个磁盘选择不同的目标存储帐户，确保在目标区域中具有相同的存储配置
标准托管磁盘 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |  
高级托管磁盘 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |
存储空间 | 支持 |         
静态加密 (SSE) | 支持 | SSE 是存储帐户的默认设置。   
适用于 Linux OS 的 Azure 磁盘加密 (ADE) | 不支持 |
热添加/移除磁盘 | 不支持 | 如果在 VM 上添加或删除数据磁盘，需要先禁用复制然后重新为 VM 启用复制。
排除磁盘 | 不支持|   默认情况下，排除临时磁盘。
存储空间直通  | 不支持|
横向扩展文件服务器  | 不支持|
LRS | 支持 |
GRS | 支持 |
RA-GRS | 支持 |
ZRS | 不支持 |  
冷存储和热存储 | 不支持 | 冷存储和热存储不支持虚拟机磁盘
虚拟网络的 Azure 存储防火墙  | 否 | 不支持访问用于存储复制数据的缓存存储帐户上特定的 Azure 虚拟网络。
常规用途 V2 存储帐户（包括热存储层和冷存储层） | 否 | 与常规用途 V1 存储帐户相比，事务成本大幅增加

<!--Not Available on Line 156 Azure Disk Encryption (ADE) for Windows OS, Reason: Encryption failed on 11/14/2018 -->
>[!IMPORTANT]
> 确保观察 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 或 [Windows](../virtual-machines/windows/disk-scalability-targets.md) 虚拟机的 VM 磁盘可伸缩性和性能目标，以避免任何性能问题。 如果遵从默认设置，Site Recovery 将基于源配置创建所需的磁盘和存储帐户。 如果自定义和选择自己的设置，请确保遵循源 VM 的磁盘可伸缩性和性能目标。

## <a name="support-for-network-configuration"></a>网络配置支持
**配置** | 支持/不支持 | **备注**
--- | --- | ---
网络接口 (NIC) | 特定 Azure VM 大小支持最大数量的 NIC | 在测试故障转移或故障转移操作过程中创建 VM 时，会创建 NIC。 故障转移 VM 上的 NIC 数取决于启用复制时源 VM 拥有的 NIC 数。 如果在启用复制后添加/删除 NIC，不会影响故障转移 VM 上的 NIC 数。
Internet 负载均衡器 | 支持 | 需要使用恢复计划中的 Azure 自动化脚本关联预配置的负载均衡器。
内部负载均衡器 | 支持 | 需要使用恢复计划中的 Azure 自动化脚本关联预配置的负载均衡器。
公共 IP| 支持 | 需要使用恢复计划中的 Azure 自动化脚本，将现有公共 IP 关联到 NIC，或创建一个公共 IP，并将其关联到 NIC。
NIC 上的 NSG（Resource Manager）| 支持 | 需要使用恢复计划中的 Azure 自动化脚本将 NSG 关联到 NIC。  
子网上的 NSG（Resource Manager 和经典）| 支持 | 需要使用恢复计划中的 azure 自动化脚本将 NSG 关联到子网。
VM 上的 NSG（经典）| 支持 | 需要使用恢复计划中的 Azure 自动化脚本将 NSG 关联到 NIC。
保留 IP（静态 IP）/保留源 IP | 支持 | 如果源 VM 上的 NIC 具有静态 IP 配置并且目标子网中包含同一 IP，则会将该 IP 分配给故障转移 VM。 如果目标子网中没有相同的 IP，则会为此 VM 保留子网中的一个 IP。 可在“复制项”>“设置”>“计算和网络”>“网络接口”中指定所选的固定 IP 。 可以选择 NIC，并指定所选的子网和 IP。
动态 IP| 支持 | 如果源 VM 上的 NIC 具有动态 IP 配置，则在默认情况下，故障转移 VM 上的 NIC 也是动态的。 可在“复制项”>“设置”>“计算和网络”>“网络接口”中指定所选的固定 IP 。 可以选择 NIC，并指定所选的子网和 IP。
流量管理器集成 | 支持 | 可以对流量管理器进行预配置，使流量定期路由到源区域中的终结点，并在故障转移时路由到目标区域中的终结点。
Azure 托管 DNS | 支持 |
自定义 DNS  | 支持 |    
未经身份验证的代理 | 支持 | 请参阅[网络指南文档。](site-recovery-azure-to-azure-networking-guidance.md)    
经过身份验证的代理 | 不支持 | 如果 VM 正在使用经过身份验证的代理进行出站连接，则不可使用 Azure Site Recovery 进行复制。    
本地站点到站点 VPN（使用或不使用 ExpressRoute）| 支持 | 确保将 UDR 和 NSG 配置为站点恢复流量不会路由到本地。 请参阅[网络指南文档。](site-recovery-azure-to-azure-networking-guidance.md)  
VNET 到 VNET 连接 | 支持 | 请参阅[网络指南文档。](site-recovery-azure-to-azure-networking-guidance.md)  
虚拟网络服务终结点 | 支持 | 不支持虚拟网络的 Azure 存储防火墙。 不支持访问用于存储复制数据的缓存存储帐户上特定的 Azure 虚拟网络。
加速网络 | 支持 | 必须在源 VM 上启用加速网络。 [了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure VM 复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)
- [复制 Azure VM](site-recovery-azure-to-azure.md)，开始对工作负荷进行保护

<!--Update_Description: update meta properties, wording update, update link -->