---
title: Azure SQL 数据库功能比较 | Microsoft Docs
description: 本文比较了不同风格的 Azure SQL 数据库中可用的 SQL Server 功能。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: bonova, sstein
manager: digimobile
origin.date: 05/10/2019
ms.date: 05/20/2019
ms.openlocfilehash: 82f612b8a8b0ef41120aacdf104bea884ee238fb
ms.sourcegitcommit: f0f5cd71f92aa85411cdd7426aaeb7a4264b3382
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2019
ms.locfileid: "65629154"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>功能比较：Azure SQL 数据库与 SQL Server

Azure SQL 数据库与 SQL Server 共享一个通用代码库。 Azure SQL 数据库支持的 SQL Server 功能取决于创建的 Azure SQL 数据库类型。 使用 Azure SQL 数据库，可以作为弹性池或单一数据库的一部分创建一个数据库。

我们会继续向 Azure SQL 数据库添加功能。 访问针对 Azure 的服务更新网页，并使用以下筛选器获取最新更新：

- 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
- 筛选为针对 SQL 数据库功能的正式发布版 [(GA) 公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Azure SQL 数据库中的 SQL Server 功能支持

下表列出了 SQL Server 的主要功能，并说明该功能是否部分或完全受支持，同时提供更详细介绍该功能的链接。

| **SQL 功能** | **受单一数据库和弹性池的支持** |
| --- | --- |
| [活动异地复制](sql-database-active-geo-replication.md) | 是 - 除超大规模之外的所有服务层级 |
| [自动故障转移组](sql-database-auto-failover-group.md) | 是 - 除超大规模之外的所有服务层级 |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [附加数据库](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 |
| [应用程序角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 |
| [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)|
| [自动备份](sql-database-automated-backups.md) | 是的。 完整备份每 7 天一次，差异备份每 12 小时一次，日志备份每 5-10 分钟一次。 |
| [自动优化（强制执行计划）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)|
| [自动优化（索引）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)|
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 是 |
| [BACPAC 文件（导出）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 请参阅 [SQL 数据库导出](sql-database-export.md) |
| [BACPAC 文件（导入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 请参阅 [SQL 数据库导入](sql-database-import.md) |
| [备份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，仅系统启动的自动备份 - 请参阅[自动备份](sql-database-automated-backups.md) |
| [内置函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 |
| [BULK INSERT 语句](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是，但是只能从充当源的 Azure Blob 存储进行。 |
| [证书和非对称密钥](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是，无法访问文件系统完成 `BACKUP` 和 `CREATE` 操作。 |
| [更改数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 |
| [更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |
| [排序规则 - 数据库](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | 是 |
| [排序规则 - 服务器/实例](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 否，始终使用默认的逻辑服务器排序规则 `SQL_Latin1_General_CP1_CI_AS`。 |
| [列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [高级层、标准层 - S3 及以上层、常规用途层和业务关键层](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [公共语言运行时 (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 |
| [包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 |
| [包含的用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 |
| [流控制语言关键字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 |
| [凭据](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是，但是仅限[数据库范围的凭据](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 |
| [跨数据库查询](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-overview.md) |
| [跨数据库事务](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 |
| [游标](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 |
| [Data compression](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)（数据压缩） | 是 |
| [数据库邮件](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 |
| [数据迁移服务 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | 是 |
| [数据库镜像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 |
| [数据库配置设置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 |
| [数据库快照](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 |
| [数据类型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |
| [DBCC 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅单个语句 |
| [DDL 语句](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大多数 - 请参阅单个语句 |
| [DDL 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 仅数据库 |
| [分布式分区视图](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 |
| [分布式事务 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 请参阅[弹性事务](sql-database-elastic-transactions-overview.md) |
| [DML 语句](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 |
| [DML 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大多数 - 请参阅单个语句 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大多数 - 请参阅单个 DMV |
| [动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)|
| [弹性池](sql-database-elastic-pool.md) | 是 |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 请参阅[警报](sql-database-insights-alerts-portal.md) |
| [表达式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 |
| [扩展的事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅 [SQL 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md) |
| [扩展的存储过程](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 |
| [文件和文件组](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 仅限主文件组 |
| [文件流](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 |
| [全文搜索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  是，但不支持第三方断字符 |
| [函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 |
| [异地还原](sql-database-recovery-using-backups.md#geo-restore) | 是 - 除超大规模之外的所有服务层级 |
| [图形处理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 是 |
| [内存中优化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 - [仅限高级层和业务关键层](sql-database-in-memory.md) |
| [JSON 数据支持](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [是](sql-database-json-features.md) |
| [语言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅单个元素 |
| [链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) |
| [日志传送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [登录名和用户](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 是，但是 `CREATE` 和 `ALTER` 登录语句不提供所有选项（没有 Windows 和服务器级别 Azure Active Directory 登录名）。 `EXECUTE AS LOGIN` 不受支持 - 请改用 `EXECUTE AS USER`。  |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 |
| [批量导入中的最小日志记录](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否 |
| [修改系统数据](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 |
| [OLE 自动化](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 |
| [联机索引操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|是|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|是，只是为了从 Azure Blob 存储导入。 |
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|是|
| [运算符](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅单个运算符 |
| [分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 |
| 公共 IP 地址 | 是的。 访问权限可以使用防火墙或服务终结点来限制。  | 
| [数据库时间点还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) |                                是 - 仅限“常规用途”和“业务关键”服务层级 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore)                                |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否 |
| [基于策略的管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates)（谓词） | 是 |
| [查询通知](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 |
| [查询性能见解](sql-database-query-performance.md) | 是 |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 是，[处于公开预览状态](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [资源调控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 |
| [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 |
| [从备份还原数据库](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 仅从自动备份 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md) |
| [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 |
| [语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 |
| [序列号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 |
| [服务器配置设置](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 |
| [SET 语句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅单个语句 |
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 是 |
| [SQL 数据同步](sql-database-get-started-sql-data-sync.md) | 是 |
| [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否，[Azure Analysis Services](https://www.azure.cn/home/features/analysis-services/) 是一项单独的 Azure 云服务。 |
| [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 请参阅 [SQL 数据库审核](sql-database-auditing.md) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 是 |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 请参阅[扩展事件](sql-database-xevent-db-diff-from-svr.md) |
| [SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [仅限事务性和快照复制订户](sql-database-single-database-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 |
| [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 |
| [系统存储函数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大多数 - 请参阅单个函数 |
| [系统存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅单个存储过程 |
| [系统表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅单个表 |
| [系统目录视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅单个视图 |
| [临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本地和数据库范围的全局临时表 |
| [临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [是](sql-database-temporal-tables.md) |
| 时区选择 | 否 |
| 威胁检测|  [是](sql-database-threat-detection.md)|
| [跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 否 |
| [变量](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 |
| [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 仅限“常规用途”和“业务关键”服务层级|
| [VNet](../virtual-network/virtual-networks-overview.md) | 部分 - 请参阅 [VNet 终结点](sql-database-vnet-service-endpoint-rule-overview.md) |
| [Windows Server 故障转移群集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
