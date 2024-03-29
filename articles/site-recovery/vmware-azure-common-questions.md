---
title: 常见问题 - 使用 Azure Site Recovery 进行 VMware 到 Azure 的灾难恢复 | Azure
description: 本文汇总了使用 Azure Site Recovery 将本地 VMware VM 灾难恢复到 Azure 时出现的常见问题
author: rockboyfor
manager: digimobile
ms.service: site-recovery
services: site-recovery
origin.date: 04/26/2019
ms.date: 06/10/2019
ms.topic: conceptual
ms.author: v-yeche
ms.openlocfilehash: 3a9c388d4f9748bc98a212a7989dbf3bc49bd34d
ms.sourcegitcommit: 440d53bb61dbed39f2a24cc232023fc831671837
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390705"
---
# <a name="common-questions---vmware-to-azure-replication"></a>常见问题 - VMware 到 Azure 的复制

本文提供将本地 VMware VM 的灾难恢复部署到 Azure 时可能遇到的常见问题的解答。 

## <a name="general"></a>常规 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 灾难恢复需用到哪些组件？

[了解](vmware-azure-architecture.md) VMware VM 灾难恢复涉及到的组件。 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>是否可以使用 Site Recovery 将 VMware VM 迁移到 Azure？

是的，除了使用 Site Recovery 设置 VMware VM 的完全灾难恢复以外，还可以使用 Site Recovery 将本地 VMware VM 迁移到 Azure。 在此场景中，可将本地 VMware VM 复制到 Azure 存储。 然后从本地故障转移到 Azure。 故障转移后，应用和工作负荷可供使用并在 Azure VM 上运行。 该过程类似于设置完全灾难恢复，不过，在迁移时，无法从 Azure 故障回复。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帐户是否需要拥有创建 VM 的权限？
如果你是订阅管理员，则已经获得了所需的复制权限。 否则，需要有权在配置 Site Recovery 时指定的资源组和虚拟网络中创建 Azure VM，并有权根据配置写入选定的存储帐户或托管磁盘。 [了解详细信息](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="what-applications-can-i-replicate"></a>可以复制哪些应用程序？
可以复制 VMware VM 中运行的、符合[复制要求](vmware-physical-azure-support-matrix.md##replicated-machines)的任何应用或工作负荷。
- Site Recovery 支持应用程序感知型复制，因此，应用可以故障转移或故障回复到智能状态。
- Site Recovery 除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。
- [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>是否可以在 Azure 上使用来宾 OS 服务器许可证？
是，Azure 软件保障客户可以使用 [Azure 混合权益](https://www.azure.cn/pricing/hybrid-use-benefit)来节省迁移到 Azure 的 **Windows Server 计算机**的许可成本，或将 Azure 用于灾难恢复。

## <a name="security"></a>安全性

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery 需要对 VMware 服务器拥有哪些访问权限？
Site Recovery 需要访问 VMware 服务器，才能够：

- 设置运行 Site Recovery 配置服务器的 VMware VM，
- 自动发现要复制的 VM。 

### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery 需要对 VMware VM 拥有哪些访问权限？

- 若要完成复制，必须在 VMware VM 上安装并运行 Site Recovery 移动服务。 可以手动部署所需的工具，或者在为 VM 启用复制时，指定 Site Recovery 应执行服务的推送安装。 
- 在复制期间，VM 将与 Site Recovery 通信，如下所述：
    - VM 通过 HTTPS 443 端口与配置服务器通信，以进行复制管理。
    - VM 通过 HTTPS 9443 端口（可修改）将复制数据发送到进程服务器。
    - 如果启用了多 VM 一致性，则 VM 通过端口 20004 相互通信。

### <a name="is-replication-data-sent-to-site-recovery"></a>复制数据是否会发送到 Site Recovery？
不会。Site Recovery 不会拦截复制的数据，也不包含 VM 上运行的组件的任何相关信息。 复制数据在 VMware 虚拟机监控程序与 Azure 存储之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据会发送到站点恢复服务。  

Site Recovery 已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

## <a name="pricing"></a>定价
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何计算 VMware 灾难恢复的大约费用？

可以使用[定价计算器](https://www.azure.cn/zh-cn/pricing/calculator)来估算使用 Site Recovery 时的费用。

若要对费用进行详细的估算，请运行适用于 [VMware](../site-recovery/site-recovery-vmware-deployment-planner-cost-estimation.md) 的部署规划器工具，并使用[成本估算报告](../site-recovery/site-recovery-vmware-deployment-planner-cost-estimation.md)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>复制到存储的费用与直接复制到托管磁盘的费用是否有任何差别？

托管磁盘的收费与存储帐户略有不同。 [详细了解](https://www.azure.cn/pricing/details/storage/)托管磁盘的定价。

## <a name="mobility-service"></a>移动服务

### <a name="where-can-i-find-the-mobility-service-installers"></a>在哪里可以找到移动服务安装程序？
安装程序位于配置服务器上的 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** 文件夹中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安装移动服务？
可以使用多种方法在要复制的每个 VM 上进行安装：
- [推送安装](vmware-physical-mobility-service-overview.md#push-installation)
- 从 UI 或 Powershell [手动安装](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)。
- 使用 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) 等部署工具进行部署。

## <a name="managed-disks"></a>托管磁盘

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery 将数据复制到何处？

Site Recovery 将本地 VMware VM 和物理服务器复制到 Azure 中的托管磁盘。
- Site Recovery 进程服务器将复制日志写入到目标区域中的缓存存储帐户。
- 这些日志用于在带有 asrseeddisk 前缀的 Azure 托管磁盘上创建恢复点。
- 故障转移时，将使用所选的恢复点来创建新的目标托管磁盘。 此托管磁盘附加到 Azure 中的 VM。
- 以前已复制到存储帐户的 VM（2019 年 3 月以前）不受影响。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>是否可将新计算机复制到存储帐户？

不可以。从 2019 年 3 月开始，在门户中只能复制到 Azure 托管磁盘。 

只能使用 PowerShell 或 REST API（版本 2018-01-10 或 2016-08-10）将新 VM 复制到存储帐户。

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>复制到托管磁盘的优点是什么？

[了解](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 如何使用托管磁盘简化灾难恢复。

### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>计算机受保护后，是否可以更改托管磁盘类型？

是的，可以轻松针对正在进行的复制[更改托管磁盘的类型](/virtual-machines/windows/convert-disk-storage)。 在更改类型之前，请确保未在托管磁盘上生成任何 SAS URL。 在 Azure 门户中转到“托管磁盘”资源，并检查“概述”边栏选项卡上是否出现了 SAS URL 横幅。 如果出现，请单击它以取消正在进行的导出。 完成后，在接下来的几分钟内更改磁盘类型。 但是，如果更改了托管磁盘类型，需要等待 Azure Site Recovery 生成全新的恢复点。 对后续的任何测试故障转移或故障转移使用新恢复点。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>是否可将复制目标从托管磁盘切换为非托管磁盘？

否，不支持从托管磁盘切换为非托管磁盘。

## <a name="replication"></a>复制

### <a name="what-are-the-replicated-vm-requirements"></a>复制的 VM 要满足哪些要求？

[详细了解](vmware-physical-azure-support-matrix.md##replicated-machines) VMware VM/物理服务器要求和支持。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？
将 VMware VM 复制到 Azure 时，复制是持续性的。

### <a name="can-i-extend-replication"></a>是否可以扩展复制？
不支持扩展扩展或链式复制。 请在[反馈论坛](https://www.azure.cn/support/contact/)中请求此功能。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可以执行脱机初始复制？
不支持此操作。 请在[反馈论坛](https://www.azure.cn/support/contact/)中请求此功能。

### <a name="what-is-asrseeddisk"></a>什么是 asrseeddisk？
对于每个源磁盘，数据将复制到 Azure 中的托管磁盘。 此磁盘带有 asrseeddisk 前缀。 其中存储了源磁盘的副本和所有恢复点快照。

### <a name="can-i-exclude-disks-from-replication"></a>是否可从复制中排除磁盘？
是的，可以排除磁盘。

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可以复制包含动态磁盘的 VM？
可以复制动态磁盘。 操作系统磁盘必须是基本磁盘。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用复制组实现多 VM 一致性，是否可以将新 VM 添加到现有复制组？
是的，可以在为新 VM 启用复制时将这些 VM 添加到现有复制组。
- 启动复制后，无法将 VM 添加到现有的复制组。
- 无法为现有的 VM 创建复制组。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>是否可以通过添加磁盘或调整磁盘大小来修改正在复制的 VM？

对于从 VMware 复制到 Azure，可以修改磁盘大小。 如果要添加新磁盘，则需要为 VM 添加磁盘并重新启用保护。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>是否可以在不影响进行中复制的情况下将本地计算机迁移到新的 vCenter Server？
否，更改 Vcenter 或迁移将影响正在进行的复制。 需要对新的 vCenter Server 设置 Site Recovery，并为计算机重新启用复制。

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>是否可以复制到在其上配置了 VNet（具有 Azure 存储防火墙）的缓存/目标存储帐户？
否，Site Recovery 不支持复制到 VNet 上的存储。

## <a name="component-upgrade"></a>组件升级

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的移动服务代理或配置服务器版本较低，并且升级失败。 我该怎么办？  

Site Recovery 遵循 N-4 支持模型。 [详细了解](../site-recovery/service-updates-how-to.md#support-statement-for-azure-site-recovery)如何从很低的版本升级。

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>在哪里可以找到 Azure Site Recovery 的发行说明/更新汇总？

[了解](site-recovery-whats-new.md)新的更新并[获取汇总信息](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>在哪里可以找到有关灾难恢复到 Azure 的升级信息？

[了解](../site-recovery/service-updates-how-to.md#between-an-on-premises-vmware-or-physical-site-to-azure)如何升级。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>每次升级后是否都需要重新启动源计算机？

尽管建议重新启动，但并非每次升级都必须这样做。 [了解详细信息](../site-recovery/service-updates-how-to.md#between-an-on-premises-vmware-or-physical-site-to-azure)。

## <a name="configuration-server"></a>配置服务器

### <a name="what-does-the-configuration-server-do"></a>配置服务器的作用是什么？

配置服务器运行本地 Site Recovery 组件，包括：
- 在本地与 Azure 之间协调通信和管理数据复制的配置服务器本身。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。 进程服务器还会在 VM 上推送安装移动服务，并执行本地 VMware VM 的自动发现。
- 处理从 Azure 进行故障回复期间生成的复制数据的主目标服务器。

[详细了解](vmware-azure-architecture.md)配置服务器组件和流程。

### <a name="where-do-i-set-up-the-configuration-server"></a>要在哪个位置设置配置服务器？
需要为配置服务器提供一个高度可用的本地 VMware VM。 对于物理服务器灾难恢复，可以在物理计算机上安装配置服务器。

### <a name="what-do-i-need-for-the-configuration-server"></a>配置服务器需要满足哪些条件？

请查看[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手动设置配置服务器，而不使用模板进行设置？
我们建议使用最新版本的 OVF 模板来[创建配置服务器 VM](vmware-azure-deploy-configuration-server.md)。 如果出于某种原因无法使用该模板（例如，无法访问 VMware 服务器），可以从门户[下载](physical-azure-set-up-source.md)安装程序文件并设置配置服务器。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>是否可将配置服务器复制到多个区域？
否。 为此，需在每个区域中设置一个配置服务器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可以在 Azure 中托管配置服务器？
虽然可以这样做，但运行配置服务器的 Azure VM 需要与本地的 VMware 基础结构和 VM 通信。 这可能会增大延迟并影响正在进行的复制。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新配置服务器？

[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新配置服务器。
- 可以在 [Azure 更新页](https://www.azure.cn/what-is-new/)中找到最新的更新信息。
- 可从门户下载最新版本。 或者，可以直接从[下载中心](https://aka.ms/asrconfigurationserver_bjb)下载最新版本的配置服务器。
- 如果你的版本比当前版本低 4 个版本，请参阅[支持声明](../site-recovery/service-updates-how-to.md#support-statement-for-azure-site-recovery)获取升级指导。

### <a name="should-i-back-up-the-configuration-server"></a>是否应备份配置服务器？
建议定期备份配置服务器。
- 若要成功进行故障回复，要故障回复的 VM 必须位于配置服务器数据库中。
- 配置服务器必须正在运行且处于连接状态。
- [详细了解](vmware-azure-manage-configuration-server.md)常见的配置服务器管理任务。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>设置配置服务器时，是否可以手动下载并安装 MySQL？
是的。 请下载 MySQL 并将其置于 **C:\Temp\ASRSetup** 文件夹中。 然后手动安装它。 设置配置服务器 VM 并接受条款后，MySQL 在“下载并安装”中将列出为“已安装”。  

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>我是否可以避免下载 MySQL 但让 Site Recovery 安装它？
是的。 请下载 MySQL 安装程序并将其置于 **C:\Temp\ASRSetup** 文件夹中。  设置配置服务器 VM 时，请接受条款，然后单击“下载并安装”。  门户将使用你添加的安装程序来安装 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>是否可以将配置服务器 VM 用于任何其他项？
否，只能将该 VM 用于配置服务器。 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以克隆配置服务器并将其用于业务流程？
否，应设置新配置服务器以避免注册问题。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>是否可以更改配置服务器注册到的保管库？
否。 将某个保管库关联到配置服务器后，无法更改该保管库。 请查看[此文](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)了解如何重新注册。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可以将同一配置服务器同时用于 VMware VM 和物理服务器的灾难恢复？
可以，但请注意，物理计算机仅可故障回复到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>在哪里可以下载配置服务器的密码？
[了解如何](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)下载通行短语。

### <a name="where-can-i-download-vault-registration-keys"></a>在哪里可以下载保管库注册密钥？

在“恢复服务保管库”中，单击“Site Recovery 基础结构” > “管理”中的“配置服务器”。    然后在“服务器”中，选择“下载注册密钥”以下载保管库凭据文件。  

## <a name="process-server"></a>进程服务器

### <a name="unable-to-select-process-server-during-enable-replication"></a>启用复制期间中无法选择进程服务器

从版本 9.24 开始，需要设置横向扩展进程服务器时，增强功能会提供[进程服务器警报](vmware-physical-azure-monitor-process-server.md#process-server-alerts)。 这是为了避免发生进程服务器限制，并避免使用不正常的进程服务器。

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>如何获取进程服务器的准确运行状况？

将 Site Recovery 组件升级到[最新版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)（至少 9.24）。

## <a name="failover-and-failback"></a>故障转移和故障回复
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>是否可以使用本地的进程服务器进行故障回复？
我们强烈建议在 Azure 中创建一个进程服务器用于故障回复，以免出现数据传输延迟。 此外，如果已在配置服务器将源 VM 网络与面向 Azure 的网络相隔离，则必须使用 Azure 中创建的进程服务器进行故障回复。

### <a name="can-i-retain-the-ip-address-on-failover"></a>是否可以在故障转移之后保留 IP 地址？
是的，可以在故障转移之后保留 IP 地址。 在故障转移之前，请确保在 VM 的“计算和网络”设置中指定目标 IP 地址。 此外，故障转移时请关闭计算机，以免故障回复期间发生 IP 地址冲突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>在故障转移之前是否可以更改目标 VM 大小或 VM 类型？
是的，在故障转移之前的任何时间，都可以在门户中已复制的 VM 的“计算和网络”设置中，更改 VM 的类型或大小。

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？
对于 VMware 到 Azure 的复制，可以使用的最早恢复点是 72 小时。

### <a name="how-do-i-access-azure-vms-after-failover"></a>故障转移后如何访问 Azure VM？
故障转移后，可以通过安全的 Internet 连接、站点到站点 VPN 或 Azure ExpressRoute 来访问 Azure VM。 在连接之前需要做许多准备。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>故障转移的数据是否有复原能力？
Azure 具有复原能力。 Site Recovery 能够根据 Azure SLA 故障转移到辅助 Azure 数据中心。 发生故障转移时，我们会确保元数据和保管库保留在为保管库选择的同一地理区域中。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
[故障转移](site-recovery-failover.md)不是自动的。 可以在门户中单击一下鼠标来启动故障转移，或者使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices) 来触发故障转移。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可以故障回复到不同位置？
可以。故障转移到 Azure 后，如果原始位置不可用，可以故障回复到不同的位置。 [了解详细信息](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>为何需要使用 VPN 或 ExpressRoute 进行故障回复？
从 Azure 故障回复时，Azure 中的数据将复制回到本地 VM，这需要提供私人访问权限。

### <a name="can-i-resize-the-azure-vm-after-failover"></a>是否可以在故障转移后调整 Azure VM 的大小？
否，无法在故障转移后更改目标 VM 的大小或类型。

## <a name="automation-and-scripting"></a>自动化和脚本

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用脚本设置复制？
是的。 可以使用 Rest API、PowerShell 或 Azure SDK 将 Site Recovery 工作流自动化。[了解详细信息](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>性能和容量
### <a name="can-i-throttle-replication-bandwidth"></a>是否可以限制复制带宽？
是的。 [了解详细信息](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>后续步骤
* [查看](vmware-physical-azure-support-matrix.md)支持要求。
* [设置](vmware-azure-tutorial.md) VMware 到 Azure 的复制。

<!-- Update_Description: update meta properties; wording update -->