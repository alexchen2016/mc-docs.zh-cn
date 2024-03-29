---
title: 故障转移组 - Azure SQL 数据库 | Microsoft Docs
description: 自动故障转移组是一项 SQL 数据库功能，可便于管理 SQL 数据库服务器中一组数据库或托管实例中所有数据库的复制和自动/协调式故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: mathoma, carlrab
manager: digimobile
origin.date: 05/06/2019
ms.date: 05/20/2019
ms.openlocfilehash: 10906dd298f5fc52c593c40c457bbf344f0cb7cd
ms.sourcegitcommit: f0f5cd71f92aa85411cdd7426aaeb7a4264b3382
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2019
ms.locfileid: "65629167"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自动故障转移组可以实现多个数据库的透明、协调式故障转移

自动故障转移组是一项 SQL 数据库功能，可用于管理 SQL 数据库服务器中的一组数据库到另一区域的复制和故障转移。 它使用的底层技术与相同[活动异地复制](sql-database-active-geo-replication.md)相同。 可以手动启动故障转移，也可以基于用户定义的策略委托 SQL 数据库服务进行故障转移。 使用后一种做法可在发生下述情况后自动恢复次要区域中的多个相关数据库：灾难性故障或其他导致主要区域中 SQL 数据库服务完全或部分丧失可用性的计划外事件。 此外，你还可以使用可读辅助数据库卸载只读查询工作负荷。 由于自动故障转移组涉及多个数据库，因此这些数据库必须在主服务器上进行配置。 故障转移组中数据库的主服务器和辅助服务器必须位于同一订阅中。 自动故障转移组支持将组中所有的数据库复制到另一个区域中唯一的辅助服务器。

> [!NOTE]
> 如果在 SQL 数据库服务器上使用单一数据库或共用数据库，并要在相同或不同的区域中使用多个辅助数据库，请使用[活动异地复制](sql-database-active-geo-replication.md)。

将自动故障转移组与自动故障转移策略配合使用时，任何影响组中一个或多个数据库的服务中断都会导致自动故障转移。 此外，自动故障转移组提供在故障转移期间保持不变的读写和只读侦听器终结点。 无论使用手动故障转移激活还是自动故障转移激活，故障转移都会将组中所有的辅助数据库切换到主数据库。 数据库故障转移完成后，会自动更新 DNS 记录，以便将终结点重定向到新的区域。 有关具体的 RPO 和 RTO 数据，请参阅[业务连续性概述](sql-database-business-continuity.md)。

将自动故障转移组与自动故障转移策略配合使用时，任何影响 SQL 数据库服务器中的数据库的服务中断都会导致自动故障转移。 可使用以下方式管理自动故障转移组：

- [Azure 门户](sql-database-implement-geo-distributed-database.md)
- [PowerShell：故障转移组](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST API：故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups)。

故障转移后，请确保在新的主机上配置服务器和数据库的身份验证要求。 有关详细信息，请参阅 [SQL Database security after disaster recovery](sql-database-geo-replication-security-config.md)（灾难恢复后的 Azure SQL 数据库安全性）。

若要真正实现业务连续性，只需添加数据中心之间的数据库冗余即可，这只是该解决方案的一部分功能。 在发生灾难性故障后，端对端地恢复应用程序（服务）需要恢复构成该服务的所有组件以及所有依赖服务。 这些组件的示例包括客户端软件（例如，使用自定义 JavaScript 的浏览器）、Web 前端、存储和 DNS。 所有组件必须能够弹性应对相同的故障，并在应用程序的恢复时间目标 (RTO) 值内变为可用，这一点非常关键。 因此，需要识别所有依赖服务，并了解它们提供的保证和功能。 然后，必须执行适当的步骤来确保对用户的服务所依赖的服务执行故障转移期间，用户的服务能够正常运行。 有关设计灾难恢复解决方案的详细信息，请参阅[设计使用活动异地复制的灾难恢复云解决方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="auto-failover-group-terminology-and-capabilities"></a>自动故障转移组的术语和功能

- **故障转移组 (FOG)**

  故障转移组是由单个 SQL 数据库服务器管理的一组数据库，当主要区域的服务中断导致所有或部分主要数据库不可用时，该组数据库可作为一个单元故障转移到另一区域。

- **SQL 数据库服务器**

     使用 SQL 数据库服务器，可以将一个 SQL 数据库服务器上的部分或所有用户数据库放入故障转移组。 此外，SQL 数据库服务器支持一个 SQL 数据库服务器上有多个故障转移组。

- **主要节点**

  托管着故障转移组中的主要数据库的 SQL 数据库服务器。

- **辅助节点**

  托管着故障转移组中的辅助数据库的 SQL 数据库服务器。 辅助节点不能与主要节点位于相同的区域。

- **将单一数据库添加到故障转移组**

  可以将同一 SQL 数据库服务器上的多个单一数据库放入同一故障转移组。 如果将单一数据库添加到故障转移组，则它会在辅助服务器上自动使用相同的版本和计算大小创建辅助数据库。  创建故障转移组时指定该服务器。 如果在辅助服务器中添加已具有辅助数据库的数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助服务器中创建新的辅助节点。
  
- **将弹性池中的数据库添加到故障转移组**

  可将一个弹性池内的所有或多个数据库放入同一故障转移组。 如果主数据库在弹性池中，将在具有相同名称的弹性池（辅助池）中自动创建辅助数据库。 必须确保辅助服务器包含名称完全相同的弹性池，并有足够的可用容量来托管将由故障转移组创建的辅助数据库。 如果在辅助池中已有辅助数据库的池中添加数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助池中创建新的辅助数据库。
  
- **故障转移组读写侦听器**

  构成的 DNS CNAME 记录，指向当前主要节点的 URL。 它允许读写 SQL 应用程序在故障转移发生后主服务器发生更改时，以透明方式重新连接到主数据库。 在 SQL 数据库服务器上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.database.chinacloudapi.cn`。

- **故障转移组只读侦听器**

  构成的 DNS CNAME 记录，指向只读侦听器，后者指向辅助节点的 URL。 它可让只读 SQL 应用程序以透明方式使用指定的负载均衡规则连接到辅助节点。 在 SQL 数据库服务器上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.secondary.database.chinacloudapi.cn`。

- **自动故障转移策略**

  默认使用自动故障转移策略配置故障转移组。 检测到故障且宽限期过后，SQL 数据库服务会触发故障转移。 系统必须确保，因影响范围太大，[SQL 数据库服务的内置高可用性基础结构](sql-database-high-availability.md)无法缓解服务中断。 如果要从应用程序控制故障转移工作流，可以关闭自动故障转移。

- **只读故障转移策略**

  默认禁用只读侦听器的故障转移功能。 这可确保在辅助数据库脱机时，主数据库的性能不会受到影响。 但是，这也意味辅助数据库恢复前，只读会话将无法连接。 如果无法容许只读会话停机，但能够以主数据库的潜在性能降级为代价将主数据库临时用于只读和读写流量，则可以为只读侦听器启用故障转移。 在这种情况下，如果辅助节点不可用，则会将只读流量自动重定向到主要节点。

- **计划的故障转移**

   将辅助角色切换为主要角色之前，计划内故障转移在主要数据库与辅助数据库之间执行完全同步。 这可以保证数据不会丢失。 计划内故障转移用于以下场景：

  - 不可接受数据丢失时在生产环境中执行灾难恢复 (DR) 演练
  - 将数据库重新定位到不同的区域
  - 缓解服务中断（故障回复）后将数据库恢复到主要区域。

- **未计划的故障转移**

   计划外故障转移或强制故障转移立即将辅助角色切换为主要角色，而不与主要节点进行任何同步。 此操作会导致数据丢失。 在服务中断期间当主要节点不可访问时，计划外故障转移将用作恢复方法。 原始主要节点重新联机后，将在不进行同步的情况下自动重新连接，并成为新的辅助节点。

- **手动故障转移**

  可随时手动启动故障转移，而不考虑自动故障转移配置。 如果未配置自动故障转移策略，则需要执行手动故障转移才能将故障转移组中的数据库恢复到辅助节点。 可通过完整数据同步启动强制或友好的故障转移。 后者可用于将主要节点重新定位到次要区域。 故障转移完成后，会自动更新 DNS 记录，以确保与新的主要节点建立连接

- **数据丢失宽限期**

  由于主数据库和辅助数据库是使用异步复制进行同步的，因此故障转移可能会导致数据丢失。 可以自定义自动故障转移策略，以便反映应用程序对数据丢失的容错。 通过配置 **GracePeriodWithDataLossHours**，可以控制系统启动可能导致数据丢失的故障转移之前的等待时间。

- **多个故障转移组**

  可为同一对服务器配置多个故障转移组以控制故障转移规模。 每个组均独立进行故障转移。 如果多租户应用程序使用弹性池，则可使用此功能来混合每个池的主数据库和辅助数据库。 采用这种方式可将服务中断的影响范围缩小到一半的租户中。
## <a name="permissions"></a>权限
通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 管理故障转移组的权限。 [SQL Server 参与者](../role-based-access-control/built-in-roles.md#sql-server-contributor)角色拥有管理故障转移组所需的全部权限。 

### <a name="create-failover-group"></a>创建故障转移组
若要创建某个故障转移组，需要对主服务器和辅助服务器，以及该故障转移组中的所有数据库拥有 RBAC 写入访问权限。

### <a name="update-a-failover-group"></a>更新故障转移组
若要更新某个故障转移组，需要对该故障转移组，以及当前主服务器上的所有数据库拥有 RBAC 写入访问权限。  

### <a name="failover-a-failover-group"></a>对故障转移组进行故障转移
若要对某个故障转移组进行故障转移，需要对新的主服务器上的故障转移组拥有 RBAC 写入访问权限。 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>有关将故障转移组与单一数据库和弹性池配合使用的最佳做法

自动故障转移组必须在主要 SQL 数据库服务器上进行配置，并会将它连接到不同 Azure 区域中的辅助 SQL 数据库服务器。  组可以包含这些服务器中的所有或部分数据库。 下图演示了使用多个数据库和自动故障转移组的异地冗余云应用程序的典型配置。

![自动故障转移](./media/sql-database-auto-failover-group/auto-failover-group.png)

在设计具有业务连续性的服务时，请遵循以下一般准则：

- **使用一个或多个故障转移组来管理多个数据库的故障转移**

  可在不同区域的两个服务器（主服务器和辅助服务器）之间创建一个或多个故障转移组。 每组可包含一个或多个数据库，这些数据库是在所有或某些主数据库因主要区域中的服务中断而变得不可用时，作为单元恢复的。 故障转移组使用服务目标作为主数据库创建异地辅助数据库。 如果将现有的异地复制关系添加到故障转移组，请确保使用与主数据库相同的服务层级和计算大小来配置异地辅助数据库。

- **使用读写侦听器处理 OLTP 工作负荷**

  执行 OLTP 操作时，请使用 `<fog-name>.database.chinacloudapi.cn` 作为服务器 URL，连接将自动定向到主要节点。 此 URL 在故障转移后不会更改。 请注意，故障转移涉及更新 DNS 记录，以便仅在刷新客户端 DNS 缓存后，客户端连接才会重定向到新的主数据库。

- **使用只读侦听器处理只读工作负荷**

  如果你有一个在逻辑上隔离的只读工作负荷，且它允许存在一些过时数据，则可在应用程序中使用辅助数据库。 对于只读的会话，请使用 `<fog-name>.secondary.database.chinacloudapi.cn` 作为服务器 URL，连接将自动定向到辅助节点。 此外，还建议使用 **ApplicationIntent=ReadOnly** 在连接字符串中指示读取意向。

- **可应对性能下降的问题**

  SQL 故障转移决策与应用程序的其余部分或所用的其他服务无关。 应用程序可能是“mixed”与在一个区域和一些在另一些组件。 为避免性能降低，请确保在 DR 区域中采用冗余的应用程序部署，并遵循这些[网络安全性准则](#failover-groups-and-network-security)。

  > [!NOTE]
  > DR 区域中的应用程序不必使用不同的连接字符串。  

- **可应对数据丢失的问题**

  如果检测到服务中断，SQL 会等待 **GracePeriodWithDataLossHours** 指定的期限。 默认值为 1 小时。 如果不能承受丢失数据，请确保设置 **GracePeriodWithDataLossHours** 到一个足够大的数字，如 24 小时。 使用手动组故障转移从辅助节点故障回复到主要节点。

  > [!IMPORTANT]
  > DTU 少于或等于 800、使用异地复制的数据库超过 250 个的弹性数据库池可能会遇到更长的计划故障转移和性能下降等问题。  这些问题更可能在写密集型工作负荷下发生，例如，异地复制终结点广泛分隔于各个地理位置，或者每个数据库使用多个辅助终结点。  当异地复制滞后随着时间推移增加时，这些问题的症状便会显现。  这种滞后可以使用 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 进行监视。  如果发生这些问题，缓解方法包括增加池 DTU 的数量或者减少同一池中异地复制数据库的数量。

## <a name="failover-groups-and-network-security"></a>故障转移组和网络安全

对于某些应用程序，安全规则要求只允许特定组件（如 VM、Web 服务等）通过网络访问数据层。此要求对业务连续性设计和故障转移组的使用提出了一些挑战。 在实施此类受限访问时，请考虑以下选项。

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用故障转移组和虚拟网络规则

如果使用[虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)来限制对 SQL 数据库的访问，请注意每个虚拟网络服务终结点仅适用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。 因此，只有部署在同一区域中的客户端应用程序才能连接到主数据库。 因为故障转移会导致 SQL 客户端会话重新路由到不同（次要）区域中的服务器，所以源自该区域之外的客户端的这些会话将失败。 因此，如果参与的服务器包含在虚拟网络规则中，则无法启用自动故障转移策略。 若要支持手动故障转移，请执行以下步骤：

1. 在次要区域中预配应用程序前端组件（Web 服务、虚拟机等）的冗余副本
2. 为主服务器和辅助服务器分别配置[虚拟网络规则](sql-database-vnet-service-endpoint-rule-overview.md)
3. 使用流量管理器配置启用[前端故障转移](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 检测到服务中断时启动手动故障转移。 此选项针对需要在前端和数据层之间保持一致延迟的应用程序进行了优化，并支持在前端和/或数据层受到服务中断的影响时进行恢复。

> [!NOTE]
> 如果使用**只读侦听器**对只读工作负荷进行负载均衡，请确保在次要区域中的 VM 或其他资源上执行此工作负荷，以便它可以连接到辅助数据库。

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>使用故障转移组和 SQL 数据库防火墙规则

如果业务连续性计划要求使用自动故障转移组进行故障转移，则可以使用传统防火墙规则限制对 SQL 数据库的访问。  若要支持自动故障转移，请执行以下步骤：

1. [创建公共 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [创建公共负载均衡器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer)并为其分配公共 IP。
3. 为前端组件[创建虚拟网络和虚拟机](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)
4. [创建网络安全组](../virtual-network/security-overview.md)并配置入站连接。
5. 使用“Sql”[服务标记](../virtual-network/security-overview.md#service-tags)确保出站连接向 Azure SQL 数据库开放。
6. 创建 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)，以允许来自步骤 1 中创建的公共 IP 地址的入站流量。

有关如何配置出站访问以及在防火墙规则中使用哪个 IP 的详细信息，请参阅[负载均衡器出站连接](../load-balancer/load-balancer-outbound-connections.md)。

上述配置将确保自动故障转移不会阻止来自前端组件的连接，并假定应用程序可以容忍前端与数据层之间的较长延迟。

> [!IMPORTANT]
> 若要保证区域服务中断的业务连续性，则必须确保前端组件和数据库的地理冗余。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升级或降级主数据库

无需断开连接任何辅助数据库，即可将主数据库升级或降级到不同的计算大小（在相同的服务层级中，但不在“常规用途”与“业务关键”类型之间）。 升级时，建议先升级所有辅助数据库，再升级主数据库。 降级时，请反转顺序：先降级主数据库，再降级所有辅助数据库。 将数据库升级或降级到不同服务层级时，将强制执行此建议操作。

具体而言，建议采用此顺序的目的是避免较低 SKU 上的辅助数据库在过载时出现问题，并且必须在升级或降级过程中重新设定种子。 此外，可以通过将主数据库设为只读来避免问题，代价是针对主数据库的所有读写工作负荷会受到影响。 

> [!NOTE]
> 如果辅助数据库是作为故障转移组配置的一个部分创建的，则我们不建议对辅助数据库进行降级。 这是为了确保激活故障转移后，数据层有足够的容量来处理常规工作负荷。

## <a name="preventing-the-loss-of-critical-data"></a>防止丢失关键数据

由于广域网的延迟时间较长，连续复制使用了异步复制机制。 在发生故障时，异步复制会不可避免地丢失某些数据。 但是，某些应用程序可能要求不能有数据丢失。 为了保护这些关键更新，应用程序开发人员可以在提交事务后立即调用 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系统过程。 调用 **sp_wait_for_database_copy_sync** 会阻止调用线程，直到将上次提交的事务传输到辅助数据库。 但是，它不会等待传输的事务提交到辅助数据库进行重播。 **sp_wait_for_database_copy_sync** 的范围是一个具体的连续复制链路。 对主数据库具有连接权限的任何用户都可以调用此过程。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 将在故障转移后防止数据丢失，但它不会保证读取访问的完全同步。 **sp_wait_for_database_copy_sync** 过程调用导致的延迟可能会很明显，具体取决于调用时的事务日志大小。

## <a name="failover-groups-and-point-in-time-restore"></a>故障转移组和时间点还原

有关将时间点还原与故障转移组配合使用的信息，请参阅[时间点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="programmatically-managing-failover-groups"></a>以编程方式管理故障转移组

如上所述，也可以使用 Azure PowerShell 和 REST API 以编程方式管理自动故障转移组和活动异地复制。 下表描述了可用的命令集。 活动异地复制包括一组用于管理的 Azure 资源管理器 API，其中包括 [Azure SQL 数据库 REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 这些 API 需要使用资源组，并支持基于角色的安全性 (RBAC)。 有关如何实现访问角色的详细信息，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell：使用单一数据库和弹性池管理 SQL 数据库故障转移

| Cmdlet | 说明 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 从服务器中移除故障转移组，并删除组中包含的所有辅助数据库 |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 检索故障转移组配置 |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改故障转移组的配置 |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 触发故障转移组到辅助服务器的故障转移 |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|将一个或多个数据库添加到 Azure SQL 数据库故障转移组|
|  | |

> [!IMPORTANT]
> 有关示例脚本，请参阅[为单一数据库配置并故障转移一个故障转移组](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
>

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API：使用单一数据库和共用数据库管理 SQL 数据库故障转移组

| API | 说明 |
| --- | --- |
| [创建或更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 创建或更新故障转移组 |
| [删除故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 从服务器中删除故障转移组 |
| [故障转移（计划内）](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 从当前主服务器故障转移到此服务器。 |
| [强制故障转移允许数据丢失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |从当前主服务器故障转移到此服务器。 此操作可能导致数据丢失。 |
| [获取故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 获取某个故障转移组。 |
| [按服务器列出故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出某个服务器中的故障转移组。 |
| [更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新某个故障转移组。 |
|  | |

## <a name="next-steps"></a>后续步骤

- 示例脚本请参阅：
  - [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [配置共用数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [针对单个数据库配置并故障转移一个故障转移组](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
- 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
- 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
- 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
