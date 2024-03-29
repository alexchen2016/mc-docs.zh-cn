---
title: SQL 数据库中的扩展事件 | Microsoft 文档
description: 介绍 Azure SQL 数据库中的扩展事件 (XEvents)，以及这些事件会话与 Microsoft SQL Server 中的事件会话的细微差别。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: jrasnik
manager: digimobile
origin.date: 12/19/2018
ms.date: 02/25/2019
ms.openlocfilehash: 5f2f4bedd33bee76207ac8fd05c72a85f60c169b
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626033"
---
# <a name="extended-events-in-sql-database"></a>SQL 数据库中的扩展事件
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

本主题说明 Azure SQL 数据库中的扩展事件与 Microsoft SQL server 中的扩展事件在实现方式上的细微差别。

- SQL 数据库 V12 在 2015 年下半年推出了扩展事件功能。
- SQL Server 自 2008 年即已推出扩展事件。
- SQL 数据库上的扩展事件功能集是强大的 SQL Server 功能子集。

*XEvents* 不是正式名称，有时在博客或其他非正式场合表示“扩展的事件”。

下面提供了 Azure SQL 数据库和 Microsoft SQL Server 的扩展事件的其他相关信息：

- [快速入门：SQL Server 中的扩展事件](https://msdn.microsoft.com/library/mt733217.aspx)
- [扩展事件](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>先决条件

本主题假设读者了解以下内容：

- [Azure SQL 数据库服务](https://www.azure.cn/home/features/sql-database/)。
- Microsoft SQL Server 中的[扩展事件](https://msdn.microsoft.com/library/bb630282.aspx)。

- 有关扩展事件的许多文档都适用于 SQL Server 和 SQL 数据库。

选择事件文件作为 [目标](#AzureXEventsTargets)时，事先了解以下项目会很有帮助：

- [Azure 存储服务](https://www.azure.cn/home/features/storage/)


- PowerShell
    - [对 Azure 存储使用 Azure PowerShell](../storage/common/storage-powershell-guide-full.md) - 提供有关 PowerShell 和 Azure 存储服务的综合信息。

## <a name="code-samples"></a>代码示例

相关主题提供了两个代码示例：


- [SQL 数据库中扩展事件的环形缓冲区目标代码](sql-database-xevent-code-ring-buffer.md)
    - 简短的 Transact-SQL 脚本。
    - 代码示例主题中强调，用完环形缓冲区目标时，应通过执行 alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` 语句释放其资源。 然后可以通过 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`添加环形缓冲区的另一个实例。


- [SQL 数据库中扩展事件的事件文件目标代码](sql-database-xevent-code-event-file.md)
    - 阶段 1 是 PowerShell，用于创建 Azure 存储容器。
    - 阶段 2 是 Transact-SQL，它使用 Azure 存储容器。

## <a name="transact-sql-differences"></a>Transact-SQL 的差异


- 在 SQL Server 上执行 [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) 命令时，请使用 **ON SERVER** 子句。 但在 SQL 数据库上，应改用 **ON DATABASE** 子句。


- **ON DATABASE** 子句也适用于 [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) 和 [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL 命令。


- 最佳做法是在 **CREATE EVENT SESSION** 或 **ALTER EVENT SESSION** 语句中包含 **STARTUP_STATE = ON** 的事件会话选项。
    - **= ON** 值支持在由于故障转移而重新配置逻辑数据库之后自动重新启动。

## <a name="new-catalog-views"></a>新的目录视图

扩展事件功能受多个[目录视图](https://msdn.microsoft.com/library/ms174365.aspx)的支持。 目录视图显示有关当前数据库中用户创建的事件会话的*元数据或定义*的信息。 视图不会返回有关活动事件会话的实例的信息。

| 目录<br/>视图的名称 | 说明 |
|:--- |:--- |
| **sys.database_event_session_actions** |返回针对事件会话的每个事件执行的每个操作所对应的行。 |
| **sys.database_event_session_events** |返回事件会话中每个事件所对应的行。 |
| **sys.database_event_session_fields** |返回针对事件和目标上显式设置的每个可自定义列所对应的行。 |
| **sys.database_event_session_targets** |返回事件会话的每个事件目标所对应的行。 |
| **sys.database_event_sessions** |返回 SQL 数据库中每个事件会话所对应的行。 |

在 Microsoft SQL Server 中，类似目录视图的名称包含 *.server\_* 而不是 *.database\_*。 名称模式类似于 **sys.server_event_%**。

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>新的动态管理视图 [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL 数据库具有支持扩展事件的[动态管理视图 (DMV)](https://msdn.microsoft.com/library/bb677293.aspx)。 DMV 显示有关 *活动* 事件会话的信息。

| DMV 的名称 | 说明 |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |返回有关事件会话操作的信息。 |
| **sys.dm_xe_database_session_events** |返回有关会话事件的信息。 |
| **sys.dm_xe_database_session_object_columns** |显示绑定到会话的对象的配置值。 |
| **sys.dm_xe_database_session_targets** |返回有关会话目标的信息。 |
| **sys.dm_xe_database_sessions** |返回划归到当前数据库的每个事件会话所对应的行。 |

在 Microsoft SQL Server 中，类似目录视图的名称不包含 *\_database* 部分，例如：

- **sys.dm_xe_sessions** 而不是名称<br/>**sys.dm_xe_database_sessions**。

### <a name="dmvs-common-to-both"></a>两者通用的 DMV
对于扩展的事件，有通用于 Azure SQL 数据库和 Microsoft SQL Server 的其他 DMV：

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>查找可用的扩展事件、操作和目标

可运行简单的 SQL **SELECT** 来获取可用事件、操作和目标的列表。

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>SQL 数据库事件会话的目标

可从 SQL 数据库上的事件会话捕获结果的目标如下：

- [环形缓冲区目标](https://msdn.microsoft.com/library/ff878182.aspx) - 在内存中短暂保存事件数据。
- [事件计数器目标](https://msdn.microsoft.com/library/ff878025.aspx) - 统计在扩展事件会话期间发生的所有事件。
- [事件文件目标](https://msdn.microsoft.com/library/ff878115.aspx) - 将完整缓冲区写入 Azure 存储容器。

[Windows 事件跟踪 (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API 不适用于 SQL 数据库上的扩展事件。

## <a name="restrictions"></a>限制

有几个安全相关的差异适用于 SQL 数据库的云环境：

- 扩展事件在单租户隔离模型中构建。 一个数据库中的事件会话无法访问另一个数据库中的数据或事件。
- 无法在 **master** 数据库的上下文中发出 **CREATE EVENT SESSION** 语句。

## <a name="permission-model"></a>权限模型

必须拥有数据库的**控制**权限才能发出 **CREATE EVENT SESSION** 语句。 数据库所有者 (dbo) 拥有**控制**权限。

### <a name="storage-container-authorizations"></a>存储容器授权

针对 Azure 存储容器生成的 SAS 令牌必须为权限指定 **rwl** 。 **rwl** 值提供以下权限：

- 读取
- 写入
- 列出

## <a name="performance-considerations"></a>性能注意事项

在某些情况下，大量使用扩展事件可能累积过多的活动内存，使整个系统无法正常运行。 因此，Azure SQL 数据库系统会动态设置和调整事件会话可以累积的活动内存量限制。 动态计算会考虑许多因素。

如果收到错误消息，指出已强制实施内存最大值，可采取以下纠正措施：

- 减少运行的并发事件会话。
- 通过对事件会话执行 **CREATE** 和 **ALTER** 语句，减少在 **MAX\_MEMORY** 子句中指定的内存量。

### <a name="network-latency"></a>网络延迟

**事件文件**目标在将数据保存到 Azure 存储 Blob 时可能会遇到网络延迟或故障。 SQL 数据库中的其他事件可能会延迟，因为它们要等待网络通信完成。 这种延迟可能会导致工作负荷变慢。

- 若要缓解这种性能风险，请避免在事件会话定义中将 **EVENT_RETENTION_MODE** 选项设为 **NO_EVENT_LOSS**。

## <a name="related-links"></a>相关链接

- [对 Azure 存储使用 Azure PowerShell](../storage/common/storage-powershell-guide-full.md)。
- [Azure 存储 Cmdlet](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [对 Azure 存储使用 Azure PowerShell](../storage/common/storage-powershell-guide-full.md) - 提供有关 PowerShell 和 Azure 存储服务的综合信息。
- [如何通过 .NET 使用 Blob 存储](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias 撰写的有关 Microsoft SQL Server 中扩展事件的博客文章](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)

可通过以下链接访问有关扩展事件的其他代码示例主题。 不过，必须定期检查所有示例，以确定这些示例是针对 Microsoft SQL Server 还是 Azure SQL 数据库。 然后，用户可以在运行示例时确定是否要做出细微的更改。

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
<!--Update_Description: update word & link-->