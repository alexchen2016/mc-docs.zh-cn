---
title: SQL 错误代码 - 数据库连接错误 | Microsoft 文档
description: '了解有关 SQL 数据库客户端应用程序的 SQL 错误代码，例如常见的数据库连接错误、数据库复制问题和常规错误。 '
keywords: SQL 错误代码, 访问 SQL, 数据库连接错误, SQL 错误代码
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 03/06/2019
ms.date: 04/08/2019
ms.openlocfilehash: 6a9aba5046879936c64599132e9e1bd7884c6ee3
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003525"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL 数据库客户端应用程序的 SQL 错误代码：数据库连接错误和其他问题

本文列出了 SQL 数据库客户端应用程序的 SQL 错误代码，包括数据库连接错误、暂时性错误（也称为暂时性故障）、资源调控错误、数据库复制问题、弹性池和其他错误。 大多数类别特定于 Azure SQL 数据库，并不适用于 Microsoft SQL Server。 另请参阅[系统错误消息](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx)。

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>数据库连接错误、暂时性错误和其他临时错误

下表涵盖了应用程序尝试访问 SQL 数据库时，可能遇到的连接丢失错误和其他暂时性错误的 SQL 错误代码。 有关如何连接到 Azure SQL 数据库的入门教程，请参阅[连接到 Azure SQL 数据库](sql-database-libraries.md)。

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>最常见的数据库连接错误和暂时性故障错误

Azure 基础结构能够在 SQL 数据库服务中出现大量工作负荷时动态地重新配置服务器。  此动态行为可能会导致客户端程序失去其与 SQL 数据库的连接。 此类错误情况称为 *暂时性故障*。

强烈建议客户端程序包含重试逻辑，以便它可以提供一段时间来让暂时性故障纠正自身，并尝试重建连接。  我们建议在第一次重试前延迟 5 秒钟。 如果在少于 5 秒的延迟后重试，云服务有超载的风险。 对于后续的每次重试，延迟应以指数级增大，最大值为 60 秒。

出现暂时性故障错误时，客户端程序通常会发出以下错误消息之一：

* 服务器 &lt;Azure_instance&gt; 上的数据库 &lt;db_name&gt; 当前不可用。 请稍后重试连接。 如果问题仍然存在，请与客户支持人员联系，并向其提供会话跟踪 ID &lt;session_id&gt;
* 服务器 &lt;Azure_instance&gt; 上的数据库 &lt;db_name&gt; 当前不可用。 请稍后重试连接。 如果问题仍然存在，请与客户支持人员联系，并向其提供会话跟踪 ID &lt;session_id&gt;。 （Microsoft SQL Server，错误：40613）
* 远程主机强行关闭了现有连接。
* System.Data.Entity.Core.EntityCommandExecutionException：执行命令定义时出错了。 有关详细信息，请参阅内部异常。 ---> System.Data.SqlClient.SqlException：在接收来自服务器的结果时发生传输级错误。 （提供程序：会话提供程序，错误：19 - 物理连接不可用）
* 到辅助数据库的连接尝试失败，因为该数据库正处于重新配置过程中，并且当在主数据库上进行活动的事务时，它正忙于应用新页。 

有关重试逻辑的代码示例，请参阅：

* [用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md) 
* [修复 SQL 数据库中的连接错误和暂时性错误的操作](sql-database-connectivity-issues.md)

[SQL Server 连接池 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx) 中提供了有关使用 ADO.NET 的客户端的*阻塞期*的说明。

### <a name="transient-fault-error-codes"></a>暂时性故障错误代码

以下错误为暂时性错误，并且应在应用程序逻辑中重试： 

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 4060 |16 |无法打开该登录请求的数据库“%.&#x2a;ls”。 登录失败。 有关详细信息，请参阅[错误 4000 到 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |该服务在处理你的请求时遇到错误。 请重试。 错误代码 %d。<br/><br/>当服务由于软件或硬件升级、硬件故障或任何其他故障转移问题而关闭时，将收到此错误。 错误 40197 的消息中嵌入的错误代码 (%d) 提供有关所发生的故障或故障转移类型的其他信息。 错误 40197 的消息中嵌入的错误代码的一些示例有 40020、40143、40166 和 40540。<br/><br/>重新连接到 SQL 数据库服务器会将你自动连接到数据库的正常运行副本。 应用程序必须捕获错误 40197、记录该消息中嵌入的错误代码 (%d) 以供进行故障排除，然后尝试重新连接到 SQL 数据库，直到资源可用且再次建立连接为止。 有关详细信息，请参阅[暂时性错误](sql-database-connectivity-issues.md#transient-errors-transient-faults)。|
| 40501 |20 个 |服务当前正忙。 请在 10 秒钟后重试请求。 事件 ID：%ls。 代码：%d。 有关详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)|
| 40613 |17 |数据库“%.&#x2a;ls”（在服务器“%.&#x2a;ls”上）当前不可用。 请稍后重试连接。 如果问题仍然存在，请与客户支持人员联系，并向其提供“%.&#x2a;ls”的会话跟踪 ID。<br/><br/> 如果已建立到数据库的现有专用管理员连接 (DAC)，则可能发生此错误。 有关详细信息，请参阅[暂时性错误](sql-database-connectivity-issues.md#transient-errors-transient-faults)。|
| 49918 |16 |无法处理请求。 没有足够的资源来处理请求。<br/><br/>服务当前正忙。 请稍后重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>|
| 49919 |16 |无法处理创建或更新请求。 订阅“%ld”有太多创建或更新操作正在进行。<br/><br/>服务正忙于为订阅或服务器处理多个创建或更新请求。 为了优化资源，当前阻止了请求。 请查询 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以了解挂起的操作。 请等到挂起的创建或更新请求完成后，或删除其中一个挂起的请求，再重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/> |
| 49920 |16 |无法处理请求。 订阅“%ld”有太多操作正在进行。<br/><br/>服务正忙于为此订阅处理多个请求。 为了优化资源，当前阻止了请求。 请查询 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以了解操作状态。 请等到挂起的请求完成，或删除其中一个挂起的请求，然后重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>|
| 4221 |16 |由于等待“HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING”的时间过长，登录以读取次要副本失败。 副本不可用于登录，因为回收副本时缺少正在进行中的事务的行版本。 可以通过回滚或提交主要副本上的活动事务来解决此问题。 通过避免在主要副本上长时间写入事务，可以将此状况的发生次数降到最低。 |

## <a name="database-copy-errors"></a>数据库复制错误

在 Azure SQL 数据库中复制数据库时，可能会发生以下错误。 有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 40635 |16 |IP 地址为“%.&#x2a;ls”的客户端已暂时禁用。 |
| 40637 |16 |创建数据库副本当前处于禁用状态。 |
| 40561 |16 |数据库复制失败。 源数据库或目标数据库不存在。 |
| 40562 |16 |数据库复制失败。 源数据库已删除。 |
| 40563 |16 |数据库复制失败。 目标数据库已删除。 |
| 40564 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40565 |16 |数据库复制失败。 不允许来自同一源的多个并发数据库复制。 请删除目标数据库，并重试。 |
| 40566 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40567 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40568 |16 |数据库复制失败。 源数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40569 |16 |数据库复制失败。 目标数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40570 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40571 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |

## <a name="resource-governance-errors"></a>资源调控错误

使用 Azure SQL 数据库时过度使用资源会造成以下错误。 例如：

* 事务打开的时间过长。
* 事务持有过多的锁。
* 应用程序耗用太多内存。
* 应用程序耗用太多 `TempDb` 空间。

相关主题：

* 有关详细信息，请参阅：
  * [数据库服务器资源限制](sql-database-resource-limits-database-server.md)
  * [单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)
  * [弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)
  * [单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)
  * [弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 10928 |20 个 |资源 ID：%d。 数据库的 %s 限制是 %d 且已达到该限制。 有关详细信息，请参阅[单一数据库和入池数据库的 SQL 数据库资源限制](sql-database-resource-limits-database-server.md)。<br/><br/>资源 ID 指示已达到限制的资源。 对于工作线程，资源 ID = 1。 对于会话，资源 ID = 2。<br/><br/>有关此错误以及如何解决此错误的详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/> |
| 10929 |20 个 |资源 ID：%d。 %s 最小保证为 %d，最大限制为 %d，数据库的当前使用率为 %d。 但是，服务器当前太忙，无法支持针对该数据库的数目大于 %d 的请求。 资源 ID 指示已达到限制的资源。 对于工作线程，资源 ID = 1。 对于会话，资源 ID = 2。 有关详细信息，请参阅： <br/>&bull; &nbsp;[数据库服务器资源限制](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>否则，请稍后再试。 |
| 40544 |20 个 |数据库已达到大小配额。 请将数据分区或删除、删除索引或查阅文档以找到可能的解决方案。 有关数据库缩放的信息，请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。|
| 40549 |16 |由于有长时间运行的事务，已终止会话。 请尝试缩短事务运行时间。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](sql-database-use-batching-to-improve-performance.md)。|
| 40550 |16 |由于会话获取的锁过多，已终止该会话。 请尝试在单个事务中读取或修改更少的行。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](sql-database-use-batching-to-improve-performance.md)。|
| 40551 |16 |由于过度使用 `TEMPDB`，已终止该会话。 请尝试修改查询以减少使用临时表空间。<br/><br/>如果在使用临时对象，则通过在会话不再需要临时对象后删除这些临时对象，可以节省 `TEMPDB` 数据库中的空间。 若要详细了解如何在 SQL 数据库中使用 tempdb，请参阅 [SQL 数据库中的 Tempdb 数据库](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
| 40552 |16 |由于过度使用事务日志空间，已终止该会话。 请尝试在单个事务中修改更少的行。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](sql-database-use-batching-to-improve-performance.md)。<br/><br/>如果在使用 `bcp.exe` 实用程序或 `System.Data.SqlClient.SqlBulkCopy` 类执行大容量插入，则可尝试使用 `-b batchsize` 或 `BatchSize` 选项来限制在各事务中复制到服务器的行数。 如果使用 `ALTER INDEX` 语句重新生成索引，请尝试使用 `REBUILD WITH ONLINE = ON` 选项。 有关 vCore 购买模型的事务日志大小的信息，请参阅： <br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>|
| 40553 |16 |由于过度使用内存，已终止该会话。 请尝试修改查询以处理更少的行。<br/><br/>在 Transact-SQL 代码中减少 `ORDER BY` 和 `GROUP BY` 操作的数目可以降低查询的内存需求。 有关数据库缩放的信息，请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。|

## <a name="elastic-pool-errors"></a>弹性池错误

以下错误与创建和使用弹性池有关：

| 错误代码 | 严重性 | 说明 | 纠正措施 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |弹性池已达到其存储限制。 弹性池的存储使用不能超过 (%d) MB。 到达弹性池的存储限制时，尝试向数据库写入数据。 有关资源限制的信息，请参阅： <br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)。 <br/> |在可能的情况下，考虑增加弹性池的 DTU 数并/或将存储添加到弹性池，以便提高其存储限制、减少弹性池中各数据库使用的存储，或者从弹性池中删除数据库。 有关弹性池缩放的信息，请参阅[缩放弹性池资源](sql-database-elastic-pool-scale.md)。|
| 10929 | 16 |%s 最小保证为 %d，最大限制为 %d，数据库的当前使用率为 %d。 但是，服务器当前太忙，无法支持针对该数据库的数目大于 %d 的请求。 有关资源限制的信息，请参阅： <br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](sql-database-vcore-resource-limits-elastic-pools.md)。 <br/> 否则，请稍后再试。 每个数据库的 DTU/vCore 最小值；每个数据库的 DTU/vCore 最大值。 弹性池中所有数据库上尝试的并发辅助进程（请求）总数超过池限制。 |在可能的情况下，考虑增加弹性池的 DTU 数或 vCores 数，以便提高其辅助角色限制，或者从弹性池中删除数据库。 |
| 40844 | 16 |弹性池中数据库“%ls”（位于服务器“%ls”上）是“%ls”版本的数据库，不能有连续的复制关系。  |不适用 |
| 40857 | 16 |找不到服务器“%ls”的弹性池，弹性池名称:“%ls”。 指定的弹性池在指定的服务器中不存在。 | 提供有效的弹性池名称。 |
| 40858 | 16 |弹性池“%ls”已存在于服务器“%ls”中。 指定的弹性池已存在于指定的 SQL 数据库服务器中。 | 提供新弹性池名称。 |
| 40859 | 16 |弹性池不支持服务层“%ls”。 进行弹性池预配时，不支持指定服务层。 |提供正确的版本，或者将服务层留空以使用默认服务层。 |
| 40860 | 16 |弹性池“%ls”和服务目标“%ls”的组合无效。 仅当资源类型指定为“ElasticPool”时，才能一起指定弹性池和服务层。 |指定正确的弹性池和服务层组合。 |
| 40861 | 16 |数据库版本“%.*ls”必须与弹性池服务层“%.* ls”相同。 数据库版本不同于弹性池服务层。 |请勿指定不同于弹性池服务层的数据库版本。  请注意，数据库版本不需要指定。 |
| 40862 | 16 |如果指定了弹性池服务目标，则必须指定弹性池名称。 弹性池服务目标没有唯一地标识弹性池。 |如果使用弹性池服务目标，则指定弹性池名称。 |
| 40864 | 16 |对于服务层“%.*ls”来说，弹性池的 DTU 数必须至少为 (%d) 个 DTU。 尝试将弹性池的 DTU 数设置为最小限制以下。 |重新尝试将弹性池的 DTU 数至少设置为最小限制。 |
| 40865 | 16 |对于服务层“%.*ls”来说，弹性池的 DTU 数不能超过 (%d) 个 DTU。 尝试将弹性池的 DTU 数设置为高出最大限制。 |重新尝试将弹性池的 DTU 数设置为不超过最大限制。 |
| 40867 | 16 |对于服务层“%.*ls”来说，每个数据库的 DTU 最大值必须至少为 (%d)。 尝试将每个数据库的 DTU 最大值设置为低于支持的限制。 | 考虑使用支持所需设置的弹性池服务层。 |
| 40868 | 16 |对于服务层“%.*ls”来说，每个数据库的 DTU 最大值不能超过 (%d)。 尝试将每个数据库的 DTU 最大值设置为超出支持的限制。 | 考虑使用支持所需设置的弹性池服务层。 |
| 40870 | 16 |对于服务层“%.*ls”来说，每个数据库的 DTU 最小值不能超过 (%d)。 尝试将每个数据库的 DTU 最小值设置为超出支持的限制。 | 考虑使用支持所需设置的弹性池服务层。 |
| 40873 | 16 |数据库数目 (%d) 和每个数据库的 DTU 最小值 (%d) 不能超过弹性池的 DTU 数 (%d)。 尝试指定弹性池中数据库的 DTU 最小值，该最小值超出弹性池的 DTU 数。 | 考虑增加弹性池的 DTU 数，或者降低每个数据库的 DTU 最小值，或者降低弹性池中数据库的数目。 |
| 40877 | 16 |除非弹性池不含任何数据库，否则不能将其删除。 弹性池包含一个或多个数据库，因此无法将其删除。 |删除弹性池中的数据库，以便删除弹性池。 |
| 40881 | 16 |弹性池“%.*ls”已达到其数据库计数限制。  对于 DTU 数为 (%d) 的弹性池，弹性池的数据库计数限制不能超出 (%d)。 达到弹性池的数据库计数限制时，尝试创建数据库或将其添加到弹性池。 | 在可能的情况下，考虑增加弹性池的 DTU 数，以便提高其数据库限制，或者从弹性池中删除数据库。 |
| 40889 | 16 |弹性池“%.*ls”的 DTU 数或存储限制不能降低，因为这样就无法为其数据库提供足够的存储空间。 尝试将弹性池的存储限制降低到其存储使用量以下。 | 考虑降低弹性池中各个数据库的存储使用量，或者从池中删除数据库以降低其 DTU 数或存储限制。 |
| 40891 | 16 |每个数据库的 DTU 最小值 (%d) 不能超过每个数据库的 DTU 最大值 (%d)。 尝试将每个数据库的 DTU 最小值设置为高于每个数据库的 DTU 最大值。 |确保每个数据库的 DTU 最小值不超过每个数据库的 DTU 最大值。 |
| TBD | 16 |弹性池中单个数据库的存储大小不能超过“%.*ls”服务层弹性池所允许的最大大小。 数据库的最大大小超过弹性池服务层允许的最大大小。 |将数据库的最大大小设置为处于弹性池服务层允许的最大大小限制范围内。 |

相关主题：

* [创建弹性池 (C#)](sql-database-elastic-pool-manage-csharp.md)
* [管理弹性池 (C#)](sql-database-elastic-pool-manage-csharp.md)
* [创建弹性池 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [监视和管理弹性池 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>常规错误

以下错误不属于前面的任何类别。

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin) 不是有效的名称，因为它包含无效字符。|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |登录失败。 该登录来自不受信任的域，不能用于 Windows 身份验证。%.&#x2a;ls（此版本的 SQL Server 不支持 Windows 登录。） |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |用户“%.&#x2a;ls”的登录失败。%.&#x2a;ls%.&#x2a;ls（用户“%.&#x2a;ls”的登录失败。） |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |用户“%.&#x2a;ls”的登录失败。 原因：该帐户已禁用。%.&#x2a;ls |
| 40014 |16 |不能在同一个事务中使用多个数据库。 |
| 40054 |16 |此版本的 SQL Server 不支持没有聚集索引的表。 创建聚集索引，并重试。 |
| 40133 |15 |此版本的 SQL Server 不支持此操作。 |
| 40506 |16 |指定的 SID 对此版本的 SQL Server 无效。 |
| 40507 |16 |不能使用此版本的 SQL Server 中的参数调用“%.&#x2a;ls”。 |
| 40508 |16 |USE 语句不支持在数据库间切换。 请使用新连接来连接到其他数据库。 |
| 40510 |16 |此版本的 SQL Server 不支持语句“%.&#x2a;ls” |
| 40511 |16 |此版本的 SQL Server 不支持内置函数“%.&#x2a;ls”。 |
| 40512 |16 |此版本的 SQL Server 不支持已过时的功能“%ls”。 |
| 40513 |16 |此版本的 SQL Server 不支持服务器变量“%.&#x2a;ls”。 |
| 40514 |16 |此版本的 SQL Server 不支持“%ls”。 |
| 40515 |16 |此版本的 SQL Server 不支持引用“%.&#x2a;ls”中的数据库和/或服务器名称。 |
| 40516 |16 |此版本的 SQL Server 不支持全局临时对象。 |
| 40517 |16 |此版本的 SQL Server 不支持关键字或语句选项“%.&#x2a;ls”。 |
| 40518 |16 |此版本的 SQL Server 不支持 DBCC 命令“%.&#x2a;ls”。 |
| 40520 |16 |此版本的 SQL Server 不支持安全对象类“%S_MSG”。 |
| 40521 |16 |此版本的 SQL Server 不支持服务器范围中的安全对象类“%S_MSG”。 |
| 40522 |16 |此版本的 SQL Server 不支持数据库主体“%.&#x2a;ls”类型。 |
| 40523 |16 |此版本的 SQL Server 不支持创建隐式用户“%.&#x2a;ls”。 请在使用该用户前显式创建它。 |
| 40524 |16 |此版本的 SQL Server 不支持数据类型“%.&#x2a;ls”。 |
| 40525 |16 |此版本的 SQL Server 不支持 WITH“%.ls”。 |
| 40526 |16 |此版本的 SQL Server 不支持“%.&#x2a;ls”行集提供程序。 |
| 40527 |16 |此版本的 SQL Server 不支持链接服务器。 |
| 40528 |16 |此版本的 SQL Server 用户不能映射为证书、非对称密钥或 Windows 登录。 |
| 40529 |16 |此版本的 SQL Server 不支持模拟上下文中的内置函数“%.&#x2a;ls”。 |
| 40532 |11 |无法打开该登录请求的服务器“%.&#x2a;ls”。 登录失败。 |
| 40553 |16 |由于过度使用内存，已终止该会话。 请尝试修改查询以处理更少的行。<br/><br/> 在 Transact-SQL 代码中减少 `ORDER BY` 和 `GROUP BY` 操作的数目有助于降低查询的内存需求。 |
| 40604 |16 |由于将超过服务器的配额，无法执行 CREATE/ALTER DATABASE。 |
| 40606 |16 |此版本的 SQL Server 不支持附加数据库。 |
| 40607 |16 |此版本的 SQL Server 不支持 Windows 登录。 |
| 40611 |16 |服务器上最多可以定义 128 个防火墙规则。 |
| 40614 |16 |防火墙规则的开始 IP 地址不能超过结束 IP 地址。 |
| 40615 |16 |无法打开此登录请求的服务器“{0}”。 不允许 IP 地址为“{1}”的客户端访问此服务器。<br /><br />要启用访问，请使用 SQL 数据库门户或在主数据库上运行 sp\_set\_firewall\_rule，以针对此 IP 地址或地址范围创建防火墙规则。 为使此更改生效，最多可能需要 5 分钟。 |
| 40617 |16 |以（规则名称）开头的防火墙规则名称过长。 最大长度为 128。 |
| 40618 |16 |防火墙规则名称不能为空。 |
| 40620 |16 |用户“%.&#x2a;ls”的登录失败。 密码更改失败。 此版本的 SQL Server 不支持在登录过程中更改密码。 |
| 40627 |20 个 |服务器“{0}”和数据库“{1}”中的操作正在进行。 请等待几分钟，然后重试。 |
| 40630 |16 |密码验证失败。 该密码太短，不符合策略要求。 |
| 40631 |16 |指定的密码过长。 密码的长度不能超过 128 个字符。 |
| 40632 |16 |密码验证失败。 该密码不够复杂，不符合策略要求。 |
| 40636 |16 |无法在此操作中使用保留的数据库名称“%.&#x2a;ls”。 |
| 40638 |16 |订阅 ID (subscription-id) 无效。 订阅不存在。 |
| 40639 |16 |请求不符合架构：（结构错误）。 |
| 40640 |20 个 |服务器遇到意外的异常。 |
| 40641 |16 |指定的位置无效。 |
| 40642 |17 |服务器当前太忙。 请稍后重试。 |
| 40643 |16 |指定的 x-ms-version 标头值无效。 |
| 40644 |14 |授权访问指定的订阅失败。 |
| 40645 |16 |服务器名称 (servername) 不能为空或 null。 它只能由小写字母“a”-“z”、数字 0-9 和连字符组成。 连字符不能位于名称的开头或结尾。 |
| 40646 |16 |订阅 ID 不能为空。 |
| 40647 |16 |订阅 (subscription-id) 不包含服务器 (servername)。 |
| 40648 |17 |执行了过多请求。 请稍后重试。 |
| 40649 |16 |指定的内容类型无效。 仅支持应用程序/xml。 |
| 40650 |16 |订阅 (subscription-id) 不存在或未准备好执行该操作。 |
| 40651 |16 |无法创建服务器，因为订阅 (subscription-id) 已禁用。 |
| 40652 |16 |无法移动或创建服务器。 订阅 (subscription-id) 将超过服务器配额。 |
| 40671 |17 |网关与管理服务之间的通信失败。 请稍后重试。 |
| 40852 |16 |无法在服务器“%.\*ls”中打开登录请求的数据库“%.\*ls”。 仅允许使用已启用安全性的连接字符串访问数据库。 若要访问此数据库，请将连接字符串修改为在服务器 FQDN 中包含“secure”。也就是说，'server name'.database.chinacloudapi.cn 应修改为 'server name'.database.`secure`.chinacloudapi.cn。 |
| 40914 | 16 | 无法打开登录时请求的服务器‘[服务器-名称]’。 不允许客户端访问服务器。<br /><br />若要修复，请考虑添加[虚拟网络规则](sql-database-vnet-service-endpoint-rule-overview.md)。 |
| 45168 |16 |SQL Azure 系统负载过小，正在设置单个 SQL 数据库服务器的并发数据库 CRUD 操作（例如创建数据库）数的上限。 在错误消息中指定的服务器已超过最大并发连接数。 请稍后重试。 |
| 45169 |16 |SQL Azure 系统负载过小，正在设置单个订阅的并发服务器 CRUD 操作（例如创建服务器）数的上限。 在错误消息中指定的订阅已超过最大并发连接数，已拒绝请求。 请稍后重试。 |

## <a name="next-steps"></a>后续步骤

* 阅读关于 [Azure SQL 数据库功能](sql-database-features.md)的信息。
* 阅读关于[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)的信息。
* 阅读关于[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)的信息。

