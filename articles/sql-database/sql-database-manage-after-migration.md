---
title: 迁移后管理单一数据库和共用数据库 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何在迁移到 Azure SQL 数据库后管理数据库。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 02/13/2019
ms.date: 04/29/2019
ms.openlocfilehash: 64532148c31354f0a632255fc39de916718b9418
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854892"
---
# <a name="new-dba-in-the-cloud---managing-your-single-and-pooled-databases-in-azure-sql-database"></a>云中的新 DBA - 管理 Azure SQL 数据库中单一和共用数据库

从传统的自我管理、自我控制环境过渡到 PaaS 环境后，一开始我们可能有点不适应。 应用开发人员或 DBA（数据库管理员）希望了解该平台中可让应用程序始终保持可用性、高效性、安全性和弹性的核心功能。 本文正好介绍了这些内容。 本文简要地整理了一下资源，并指导如何充分利用单一数据库和共用 SQL 数据库的主要功能在云中管理应用程序，使应用程序保持高效运行并获得最佳效果。 本文主要面向：

- 正在评估向 Azure SQL 数据库迁移应用程序（使应用程序现代化）的人员。
- 正在迁移应用程序 - 现行迁移方案。
- 最近已完成到 Azure SQL 数据库的迁移 - 云中的新 DBA。

本文讨论 Azure SQL 数据库的一些核心特性。作为一个平台，Azure SQL 数据库非常便于你在使用单一数据库和弹性池中的共用数据库时加以利用。 这些特征包括：

- 业务连续性和灾难恢复 (BCDR)
- 安全性与符合性
- 智能数据库监视和维护
- 数据移动

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>业务连续性和灾难恢复 (BCDR)

发生灾难时，可以借助业务连续性和灾难恢复功能使业务像平时一样继续。 灾难可能是数据库级别的事件（例如，某人错误地删除了某个重要表）或数据中心级别的事件（区域性灾难，例如海啸）。

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>如何在 SQL 数据库中创建和管理备份

不要在 Azure SQL 数据库中创建备份，因为没有这个必要。 SQL 数据库会自动备份数据库，因此我们不再需要考虑如何计划、执行和管理备份。 该平台每周创建完整备份，每隔几小时创建差异备份，每隔 5 分钟创建日志备份，以确保灾难恢复的有效性，并尽量减少数据丢失。 创建数据库后，首次完整备份会立即发生。 在称为“保留期”的某段时间内，这些备份均可用，可用情况因所选服务层级而有所不同。 在 SQL 数据库中，可以使用[时间点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) 还原到此保留期内的任意时间点。

|服务层|保留期（天）|
|---|:---:|
|基本|7|
|标准|35|
|高级|35|
|||

此外，使用[长期保留 (LTR)](sql-database-long-term-retention.md) 功能，还可将备份文件特意保留更长时间（最长达 10 年），且可在该期间任意时间点通过备份还原数据。 此外，数据库备份保存在异地复制存储中，可确保从区域性灾难中复原。 还可以在保留期内的任意时间点恢复 Azure 区域中的任何备份。 请参阅[业务连续性概述](sql-database-business-continuity.md)。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>如果发生数据中心级灾难或地区灾难，如何确保业务连续性

数据库备份存储在异地复制的存储中，确保在发生区域性灾难时，可将备份还原到另一个 Azure 区域。 这称为异地还原。 此方案的 RPO（恢复点目标）通常小于 1 小时，ERT（估计恢复时间）通常为几分钟到几小时。

对于任务关键型数据库，Azure SQL 数据库提供活动异地复制。 实质上，此方案的工作原理是在另一个区域创建原始数据库的异地复制辅助副本。 例如，如果数据库最初托管在 Azure 中国北部区域，而你希望在发生区域性灾难时具有复原能力。 那么，可以在中国东部或其他区域创建中国北部数据库的活动异地副本。 当中国北部发生灾难时，可以故障转移到中国东部区域。 最好将它们配置到自动故障转移组中，因为这样可以确保在发生灾难时数据库自动故障转移到美国东部的辅助数据库。 此方案的 RPO 小于 5 秒，ERT 小于 30 秒。

如果没有配置自动故障转移组，那么你的应用程序需要主动监视灾难，并启动向辅助数据库的故障转移。 可以在不同的 Azure 区域中最多创建 4 个此类活动异地副本。 这样，效果会更好。 还能以只读方式访问这些辅助活动异地副本。 这样可以非常方便地减少异地分布式应用程序方案的延迟。

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>我的灾难恢复计划如何从本地转变为 SQL 数据库

概括而言，传统的本地 SQL Server 设置要求使用故障转移群集、数据库镜像、事务复制或日志传送功能来主动管理可用性，并维护和管理备份以确保业务连续性。 使用 SQL 数据库时，平台会自动管理这些任务，因此，你可以专注于开发和优化数据库应用程序，而无需过多考虑灾难管理。 可以配置备份和灾难恢复计划，操作时只需在 Azure 门户中点击几下（或者使用 PowerShell API 执行几个命令）。

若要了解有关灾难恢复的详细信息，请参阅：[Azure SQL DB 灾难恢复 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>安全性与符合性

SQL 数据库严肃对待安全性和隐私性。 SQL 数据库中的安全性在数据库级别和平台级别实施，在划分为多个层后最好理解。 在每个层，可以控制和提供应用程序的最佳安全性。 这些层包括：

- 标识和身份验证（[Windows/SQL 身份验证和 Azure Active Directory [AAD] 身份验证](sql-database-control-access.md)。）
- 监视活动（[审核](sql-database-auditing.md)和[威胁检测](sql-database-threat-detection.md)）。
- 保护实际数据（[透明数据加密 [TDE]](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 和 [Always Encrypted [AE]](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)）。 
- 控制对敏感和特权数据的访问（[行级安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和[动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)）。

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>SQL 数据库中提供哪些用户身份验证方法

SQL 数据库中提供[两种身份验证方法](sql-database-control-access.md#authentication)：

- [Azure Active Directory 身份验证](sql-database-aad-authentication.md)
- SQL 身份验证

不支持传统的 Windows 身份验证。 Azure Active Directory (AD) 是集中式的标识和访问管理服务。 使用此服务可以十分方便地为组织的所有人员提供单一登录访问 (SSO)。 这意味着，为简化身份验证，凭据将在所有 Azure 服务之间共享。 AAD 支持 [MFA（多重身份验证）](sql-database-ssms-mfa-authentication.md)，只需[点击几下鼠标](../active-directory/hybrid/how-to-connect-install-express.md)，AAD 就能与 Windows Server Active Directory 集成。 SQL 身份验证的工作方式与以往并无不同。 提供用户名/密码后，即可对给定 SQL 数据库服务器上任何数据库的用户进行身份验证。 此外，还允许 SQL 数据库和 SQL 数据仓库在 Azure AD 域中提供多重身份验证和来宾用户帐户。 如果你已经有一个本地 Active Directory，则可以将该目录与 Azure Active Directory 联合在一起，以将目录扩展到 Azure。

|**如果你…**|**SQL 数据库/SQL 数据仓库**|
|---|---|
|不想在 Azure 中使用 Azure Active Directory (AD)|使用 [SQL 身份验证](sql-database-security-overview.md)|
|在本地 SQL Server 上使用 AD|[将 AD 与 Azure AD 联合](../active-directory/hybrid/whatis-hybrid-identity.md)，并使用 Azure AD 身份验证。 借此，你可以使用单一登录。|
|需要实施多重身份验证 (MFA)| 使用[支持 MFA 的 Azure AD 通用身份验证](sql-database-ssms-mfa-authentication.md)。|
|使用来自联合域的 Azure AD 凭据登录到 Windows|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|使用来自未与 Azure 联合的域的凭据登录到 Windows|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|具有需要连接到 SQL 数据库或 SQL 数据仓库的中间层服务|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>如何限制或控制对数据库的连接访问

你可以自行使用多种方法来获得应用程序的最佳连接组织方式。

- 防火墙规则
- VNet 服务终结点
- 保留 IP

#### <a name="firewall"></a>防火墙

防火墙仅允许特定实体访问 SQL 数据库服务器，以此来防止外部实体访问服务器。 默认拒绝 SQL 数据库服务器内的所有连接和数据库，来自其他 Azure 服务的连接除外。 使用防火墙规则，可以只对批准的实体（例如开发人员计算机）开放服务器的访问，并允许该计算机的 IP 地址通过防火墙。 利用它还可以指定你想要允许访问 SQL 数据库服务器的 IP 范围。 例如，可以在防火墙设置页中指定范围，一次性添加组织中的多个开发人员计算机 IP 地址。

可以在服务器级别或数据库级别创建防火墙规则。 可使用 Azure 门户或通过 SSMS 创建服务器级 IP 防火墙规则。 有关如何设置服务器级和数据库级防火墙规则的详细信息，请参阅：[在 SQL 数据库中创建 IP 防火墙规则](sql-database-security-tutorial.md#create-firewall-rules)。

#### <a name="service-endpoints"></a>服务终结点

默认情况下，SQL 数据库配置为“允许 Azure 服务访问服务器”- 这表示 Azure 中的所有虚拟机都可以尝试连接到数据库。 这些尝试仍需经过身份验证。 但是，如果不想让任何 Azure IP 都可访问数据库，则可禁用“允许 Azure 服务访问服务器”。 此外，还可配置 [VNet 服务终结点](sql-database-vnet-service-endpoint-rule-overview.md)。

通过服务终结点 (SE) 可以仅向自己在 Azure 中的专用虚拟网络公开关键 Azure 资源。 以此从根本上阻止了对资源的公共访问。 虚拟网络与 Azure 间的流量位于 Azure 主干网络上。 无 SE 时，可获得强制隧道数据包路由。 虚拟网络强制组织的 Internet 流量和 Azure 服务流量通过相同的路由。 借助服务终结点，可优化这进程，因为数据包直接从虚拟网络流向 Azure 主干网络上的服务。

![VNet 服务终结点](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>保留 IP

另一种方法是为 VM 设置[保留 IP](../virtual-network/virtual-networks-reserved-public-ip.md)，并将服务器防火墙设置中的那些特定 VM IP 地址列入允许列表。 通过分配保留 IP，就可以避免通过更改 IP 地址来更新防火墙规则的麻烦。

### <a name="what-port-do-i-connect-to-sql-database-on"></a>要通过哪个端口连接到 SQL 数据库

端口 1433。 SQL 数据库通过此端口通信。 若要从企业网络内部建立连接，必须在组织的防火墙设置中添加出站规则。 作为一项准则，应避免在 Azure 边界外部公开端口 1433。 可以使用 [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps) 在 Azure 中运行 SSMS。 此操作不需要与端口 1433 端口建立出站连接，因为 IP 是静态的，数据库可以只对 RemoteApp 开放，并支持多重身份验证 (MFA)。

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>如何监控服务器上以及 SQL 数据库中的数据库上的活动

#### <a name="sql-database-auditing"></a>SQL 数据库审核

使用 SQL 数据库时，可以启用“审核”以跟踪数据库事件。 [SQL 数据库审核](sql-database-auditing.md)记录数据库事件，并将事件写入 Azure 存储帐户中的审核日志文件。 若要洞察潜在的安全和策略违规、保持合规性或实现其他类似目的，审核功能特别有用。使用审核可以定义和配置你认为需要审核的事件类别，而基于这些类别，可以获取预配置的报告和仪表板来大致了解数据库中发生的事件。 可以在数据库级别或服务器级别应用这些审核策略。 有关如何启用服务器/数据库审核的指南，请参阅：[启用 SQL 数据库审核](sql-database-security-tutorial.md#enable-security-features)。

#### <a name="threat-detection"></a>威胁检测

使用[威胁检测](sql-database-threat-detection.md)可以轻松地对“审核”功能发现的安全或策略违规采取措施。 无需安全方面的专业知识即可解决系统中的潜在威胁或违规。 威胁检测还提供一些内置功能，例如 SQL 注入检测。 SQL 注入是指尝试改动或破坏数据，这是攻击数据库应用程序的一种常见手段。 威胁检测运行多组算法，这些算法可以检测潜在漏洞和 SQL 注入攻击，以及异常的数据库访问模式（如来自异常位置或不熟悉主体的访问）。 如果在数据库中检测到威胁，安全管理人员或其他指定管理员将收到电子邮件通知。 每个通知都会提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。 若要了解如何启用威胁检测，请参阅：[启用威胁检测](sql-database-security-tutorial.md#enable-security-features)。

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>如何在 SQL 数据库中对数据采取常规保护

加密是防范入侵者盗取敏感数据的强大机制。 如果入侵者没有解密密钥，已加密的数据对他们而言毫无用处。 因此，它在 SQL 数据库内置的现有安全层之上再增加了一个保护层。 保护 SQL 数据库中的数据时，需要考虑两个方面：

- 数据和日志文件中的静态数据
- 正在处理的数据

在 SQL 数据库中，默认情况下，存储子系统上的数据和日志文件中的静态数据始终完全通过[透明数据加密 [TDE]](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 进行加密。 备份也会加密。 使用 TDE 时，不需要在访问此数据的应用程序端进行更改。 顾名思义，加密和解密以透明方式进行。 为了保护处理中和静态的敏感数据，SQL 数据库提供一项称作 [Always Encrypted (AE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 的功能。 AE 是客户端加密的一种形式，它对数据库中的敏感列进行加密（因此，它们位于数据库管理员和未授权用户的已加密文本中）。 服务器首先接收加密的数据。 Always Encrypted 的密钥也存储在客户端，因此只有授权的客户端可以解密敏感列。 服务器和数据管理员无法看到敏感数据，因为加密密钥存储在客户端上。 AE 对表中的敏感列进行端到端加密，以防未经授权的客户端访问物理磁盘。 AE 目前支持等式比较，因此数据库管理员可以继续查询加密列，这是其 SQL 命令一部分。 Always Encrypted 可以与各种密钥存储选项结合使用，如 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 证书存储和本地硬件安全模块。

|**特征**|**Always Encrypted**|**透明数据加密**|
|---|---|---|
|**加密范围**|端到端|静态数据|
|**数据库服务器可以访问敏感数据**|否|是，因为加密针对静态数据|
|**允许的 T-SQL 操作**|等式比较|所有 T-SQL 外围应用可用|
|**使用此功能所要做出的应用更改**|最少|很少|
|**加密粒度**|列级别|数据库级别|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>如何限制对数据库中敏感数据的访问

每个应用程序在数据库中都有一个特定的敏感数据位，需要防止向任何人透露该位。 组织中的特定人员需要查看此数据，但是，其他人不应能够查看此数据。 一个示例是员工工资。 经理需要访问其下属的工资信息，但是，单个团队成员不应有权访问其同级的工资信息。 另一种情况是数据开发人员在开发或测试阶段可能要与敏感数据（例如客户的 SSN）交互。 同样，不需要向开发人员公开此信息。 在此情况下，敏感数据需要掩码，或者根本不公开。 SQL 数据库提供以下两种方案用于防止未经授权的用户查看敏感数据：

[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)是一种数据掩码功能，它通过掩码应用层上的非特权用户来限制敏感数据的公开。 可以定义屏蔽规则来创建屏蔽模式（例如，只显示国家/地区 ID 号的最后 4 位数：XXX-XX-0000，并将大部分编号标记为 X），并确定哪些用户被排除在屏蔽规则之外。 掩码是即时发生的，对于不同的数据类别，可以使用不同的掩码功能。 动态数据掩码可以自动检测数据库中的敏感数据并对其应用掩码。

使用[行级安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)可在行级别控制访问。 这意味着，可以根据执行查询的用户（组成员或执行上下文）来隐藏数据库表中的某些行。 访问限制是在数据库层上完成的，而不是在应用层，以此简化你的应用逻辑。 首先创建筛选器谓词，过滤掉不要公开的行和安全策略，然后定义有权访问这些行的用户。 最后，最终用户运行其查询，根据该用户的特权，他们可以或者无法查看这些受限制的行。

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>如何在云中管理加密密钥

有用于 Always Encrypted（客户端加密）和透明数据加密（静态加密）的密钥管理选项。 建议定期轮换加密密钥。 轮换频率应与内部组织法规与符合性要求一致。

#### <a name="transparent-data-encryption-tde"></a>透明数据加密 (TDE)

TDE 中有一个双密钥层次结构 - 每个用户数据库中的数据都通过对称 AES-256 数据库唯一的数据库加密密钥 (DEK) 进行加密，该密钥反过来又由服务器唯一的不对称 RSA 2048 主密钥进行加密。 主密钥的管理方式可以是：

- 由平台 - SQL 数据库自动管理。
- 由你使用 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)（用作密钥存储）进行管理。

为方便起见，透明数据加密的主密钥默认由 SQL 数据库服务托管。 如果组织想要控制主密钥，可以使用 Azure Key Vault (sql-database-always-encrypted-azure-key-vault.md) 作为密钥存储。 通过使用 Azure Key Vault，组织假定控制密钥预配、轮换使用和权限控制。 [轮换或切换 TDE 主密钥类型](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)非常快，因为它仅重新加密 DEK。 对于安全性与数据管理角色分开的组织来说，安全管理员可以为 Azure Key Vault 中的 TDE 主密钥预配密钥材料，并为数据库管理员提供 Azure Key Vault 密钥标识符，用于在服务器上进行静态加密。 Key Vault 的设计可确保 Microsoft 不会看到或提取任何加密密钥。 此外，可对组织的密钥进行集中式管理。

#### <a name="always-encrypted"></a>始终加密

Always Encrypted 中还有[两个密钥层次结构](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) - 一列敏感数据通过 AES 256 列加密密钥 (CEK) 加密，而该密钥反过来又由列主密钥 (CMK) 进行加密。 为 Always Encrypted 提供的客户端驱动程序在 CMK 长度上没有任何限制。 CEK 的加密值存储在数据库中，而 CMK 存储在受信任的密钥存储库中，如 Windows 证书存储、Azure Key Vault 或硬件安全模块。 

- [CEK 和 CMK](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) 都可轮换使用。
- CEK 轮换是有一定规模的数据操作，根据包含加密列的表大小，此操作可能十分耗时。 因此，明智的做法是相应地规划 CEK 轮换。
- 但是，CMK 轮换不会影响数据库性能，并可以使用单独的角色来完成。

下图显示了 Always Encrypted 中的列主密钥的密钥存储选项

![Always Encrypted CMK 存储提供程序](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>如何优化和保护组织与 SQL 数据库之间的流量

通常，组织与 SQL 数据库之间的网络流量是通过公共网络路由的。 但是，如果选择优化此路径并使它更安全，可以考虑 Express Route。 实质上，Express Route 是通过专用连接将企业网络扩展到 Azure 平台中。 这样，就不需要经由公共 Internet。 此外，还可以提高安全性、可靠性并优化路线，与平时使用公共 Internet 时相比，网络延迟会降低，速度会大大加快。 如果打算在组织与 Azure 之间传输大量的数据区块，则使用 Express Route 可以产生成本效益。 可以选择三种不同的连接模型在组织与 Azure 之间建立连接：

- [云交换共置](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [任意到任意](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [点到点](../expressroute/expressroute-connectivity-models.md#Ethernet)

使用 Express Route 还能将购买的带宽限制提升 2 倍。 还可以使用 Express Route 来配置跨区域连接。 若要查看 ER 连接提供商列表，请参阅：[Express Route 合作伙伴和对等互连位置](../expressroute/expressroute-locations.md)。 以下文章更详细介绍了 Express Route：

- [Express Route 简介](../expressroute/expressroute-introduction.md)
- [先决条件](../expressroute/expressroute-prerequisites.md)
- [工作流](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL 数据库是否符合任何规章要求，这对我组织的合规性有什么帮助

SQL 数据库符合一系列合规要求。 若要查看 SQL 数据库符合的最新一组合规要求，请访问 [Azure 信任中心](https://www.trustcenter.cn)，并向下钻取对你的组织而言至关重要的合规性，以了解 SQL 数据库是否包含在合规的 Azure 服务中。 需要注意的是，尽管 SQL 数据库可能被认证为合规服务，它有助于确保组织服务的符合性，但不会自动保证这一点。

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>迁移后的智能数据库监视和维护

将数据库迁移到 SQL 数据库之后，需要监视数据库（例如，检查资源利用率的大致情况，或执行 DBCC 检查）和执行定期维护（例如，重新生成或重新组织索引、统计信息等）。 幸运的是，SQL 数据库是智能化的，即，它可以使用历史趋势和记录的指标与统计信息来主动帮助你监视和维护数据库，使应用程序始终以最佳方式运行。 在某些情况下，Azure SQL 数据库可以根据配置设置自动执行维护任务。 监视 SQL 数据库中的数据库需要考虑三个方面：

- 性能监视和优化。
- 安全优化。
- 成本优化。

### <a name="performance-monitoring-and-optimization"></a>性能监视和优化

通过查询性能见解，可获得专门针对数据库工作负载的建议，以便应用程序始终以最佳运行状态。 还可以进行相应的设置，以便自动应用这些建议，避免干扰维护任务的执行。 使用索引顾问，可以根据工作负荷自动实施索引建议 - 称为“自动优化”。 建议会根据应用程序工作负荷的变化而不断改进，以提供最有价值的建议。 还可以选择手动审查这些建议，并根据自己的判断应用这些建议。  

### <a name="security-optimization"></a>安全优化

SQL 数据库提供可行的安全建议，帮助保护数据，威胁检测可用于识别和调查可能对数据库构成威胁的可疑数据库活动。 [漏洞评估](sql-vulnerability-assessment.md)是一项数据库扫描和报告服务，允许你大规模监视数据库的安全状态、识别安全风险以及偏离你定义的安全基线的行为。 每次扫描之后，都会提供可行步骤和修正脚本的自定义列表，还会提供有助于你满足符合性要求的评估报告。

Azure 安全中心不时地传送安全建议，只需点击一下鼠标就能应用这些建议。

### <a name="cost-optimization"></a>成本优化

Azure SQL 平台会分析服务器中数据库的利用率历史记录，作出评估并给出成本优化建议。 此分析通常每隔两周执行一次，生成可行的建议。 弹性池就是这样的一个选项。 建议以横幅形式显示在门户上：

![弹性池建议](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

也可以在“顾问”部分下面查看此分析：

![弹性池建议 - 顾问](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>如何在 SQL 数据库中监视性能和资源利用率

在 SQL 数据库中，可以利用平台的智能见解来监视性能并相应地进行优化。 可以使用以下方法监视 SQL 数据库中的性能和资源利用率：

#### <a name="azure-portal"></a>Azure 门户

Azure 门户通过选择数据库并单击“概述”窗格中的图表来显示数据库的利用率。 可以修改图表以显示多个指标，包括 CPU 百分比、DTU 百分比、数据 IO 百分比、会话百分比和数据库大小百分比。

![监视图表](./media/sql-database-manage-after-migration/monitoring-chart.png)

![监视图表 2](./media/sql-database-manage-after-migration/chart.png)

在此图表中，还可以按资源配置警报。 通过这些警报，可以使用电子邮件响应资源状态，写入 HTTPS/HTTP 终结点或执行操作。 有关详细信息，请参阅[创建警报](sql-database-insights-alerts-portal.md)。

#### <a name="dynamic-management-views"></a>动态管理视图

可以查询 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 动态管理视图，以返回最近一个小时的资源使用统计信息历史记录，也可以查询 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 系统目录视图，返回过去 14 天的历史记录。

#### <a name="query-performance-insight"></a>Query Performance Insight

可以使用[查询性能见解](sql-database-query-performance.md)查看特定数据库那些排名靠前的资源消耗查询和长时间运行查询的历史记录。 可以根据资源利用率、持续时间和执行频率快速识别排名靠前的查询。 可以跟踪查询和检测回归。 此功能需要为数据库启用和激活[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>我注意到了性能问题：我的 SQL 数据库故障排除方法与 SQL Server 有何不同

诊断查询和数据库性能问题时所用的大多数故障排除方法是相同的。 毕竟云由同一个 SQL Server 引擎驱动。 但是，Azure SQL DB 平台具有内置的“智能”。 它可以帮助你更轻松地排查和诊断性能问题。 此外，它还可以替你执行一些纠正措施；在某些情况下，会自动地主动修复问题。

结合使用 [Query Performance Insight (QPI)](sql-database-query-performance.md) 和[数据库顾问](sql-database-advisor.md)等智能功能对排查性能问题很有用，因此，故障排除方法的差别也体现在这方面 - 不再需要手动找出可能有助于排除手头问题的重要细节。 平台能够自动解决棘手的工作。 一个例子就是 QPI。 使用 QPI 可以一路深化到查询级别，查看历史趋势并判断查询回归的确切时间。 数据库顾问可针对缺少索引、删除索引、参数化查询等方面提供建议，帮助提高总体性能。

进行性能故障排除时，请务必确定是应用程序，还是支持它的数据库影响了应用程序的性能。 通常，性能问题出现在应用程序层。 问题原因可能在于体系结构或数据访问模式。 例如，假设某个频繁通信的应用程序对网络延迟很敏感。 在这种情况下，由于有许多简短请求在应用程序与服务器之间来回传送（“琐碎 I/O”），因此应用程序的性能会受到影响；在拥塞的网络上，往返次数会快速增加。 若要在此情况下提高性能，可以使用[批处理查询](sql-database-performance-guidance.md#batch-queries)。 使用批处理可以带来很大的帮助，因为现在请求会在批中处理；因此，可帮助减少往返延迟并提高应用程序的性能。

此外，如果注意到数据库总体性能下降，则可以监视 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 动态管理视图，了解 CPU、IO 和内存消耗情况。 由于数据库的资源严重不足，因此性能可能受到影响。 你可能会需要根据工作负荷需求的增加和减少来更改计算大小和/或服务层级。

有关优化性能问题的全套建议，请参阅：[优化数据库](sql-database-performance-guidance.md#tune-your-database)。

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>如何确保我使用的是适当的服务层级和计算大小

SQL 数据库提供了各种服务层级：“基本”、“标准”和“高级”。 在每个服务层级，将获得与该服务层级对应的有保障且可预测的性能。 根据工作负荷情况，活动可能会激增，资源利用率可能会达到当前所处计算大小的上限。 对于这种情况，最好是先评估任何优化措施（例如，添加或更改索引等）是否有所帮助。 如果仍然存在限制问题，请考虑转移到更高的服务层级或计算大小。

|**服务层**|**常见用例方案**|
|---|---|
|**基本**|用户较少，数据库不具有高并发性、扩展性和性能要求的应用程序。 |
|**标准**|并发性、规模和性能要求较高且结合了中低 IO 需求的应用程序。 |
|**高级**|具有大量并发用户、高 CPU/内存和高 IO 需求的应用程序。 高并发性、高吞吐量和对延迟敏感的应用可利用高级等级。 |
|||

为确保处于正确的计算大小，可通过“如何监视 SQL 数据库中的性能和资源利用率”中所述的某种方式来监视查询和数据库资源消耗。 如果发现查询/数据库一直在过度消耗 CPU/内存等，则可考虑纵向扩展到更高的计算大小。 同样，如果你发现，即使在繁忙时间，似乎也没有使用多少资源，则可考虑下调当前计算大小。

如果有 SaaS 应用模式或数据库整合方案，则考虑使用弹性池进行成本优化。 弹性池是实现数据库整合和成本优化的极佳方式。 若要了解有关使用弹性池管理多个数据库的详细信息，请参阅：[管理池和数据库](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)。

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>需要以何种频率对数据库运行数据库完整性检查

SQL 数据库使用某些智能技术来自动处理特定类型的数据损坏，且不丢失任何数据。 这些技术已内置在服务中，由服务根据需要利用。 SQL 数据库会定期通过还原整个服务中的数据库备份并对其运行 DBCC CHECKDB，以对其进行测试。 如果存在问题，SQL 数据库会主动解决问题。 它利用[自动页面修复](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)来修复已损坏或出现数据完整性问题的页面。 始终使用可验证页面完整性的默认 CHECKSUM 设置来验证数据库页面。 SQL 数据库主动监视并审查数据库的数据完整性，如果出现问题，则以最高优先级解决问题。 除此之外，可以根据需要选择运行自己的完整性检查。  有关详细信息，请参阅 [SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>迁移后的数据移动

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>如何从 SQL 数据库将数据作为 BACPAC 文件导出和导入

- **导出**：可以将 Azure SQL 数据库作为 BACPAC 文件从 Azure 门户导出

   ![数据库导出](./media/sql-database-export/database-export1.png)

- **导入**：还可使用 Azure 门户将数据作为 BACPAC 文件导入到数据库。

   ![数据库导入](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>如何在 SQL 数据库与 SQL Server 之间同步数据

可通过多种方法实现此目的：

- **[数据同步](sql-database-sync-data.md)** - 此功能可帮助你在多个本地 SQL Server 数据库和 SQL 数据库之间双向同步数据。 若要与本地 SQL Server 数据库同步，需要在本地计算机上安装并配置同步代理，并打开出站 TCP 端口 1433。
- **[事务复制](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** - 使用事务复制功能，可将数据从本地同步到 Azure SQL DB，本地服务器作为发布服务器，而 Azure SQL DB 作为订阅服务器。 目前仅支持此设置。 有关如何以最少停机时间将数据从本地迁移到 Azure SQL 的详细信息，请参阅：[使用事务复制](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>后续步骤

了解 [SQL 数据库](sql-database-technical-overview.md)。
