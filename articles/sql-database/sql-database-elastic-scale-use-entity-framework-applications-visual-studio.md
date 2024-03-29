---
title: 将弹性数据库客户端库与实体框架配合使用 | Microsoft 文档
description: 将弹性数据库客户端库和实体框架用于数据库编码
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 01/04/2019
ms.date: 02/25/2019
ms.openlocfilehash: 0ddfbbbc1d945294d90be640ea1906f66037dc8a
ms.sourcegitcommit: 5ea744a50dae041d862425d67548a288757e63d1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56663669"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>弹性数据库客户端库与实体框架

此文档介绍与[弹性数据库工具](sql-database-elastic-scale-introduction.md)集成所需的实体框架应用程序中的更改。 重点是使用 Entity Framework **Code First** 方法撰写[分片映射管理](sql-database-elastic-scale-shard-map-management.md)和[数据相关路由](sql-database-elastic-scale-data-dependent-routing.md)。 EF 的 [Code First – 新数据库](https://msdn.microsoft.com/data/jj193542.aspx)教程在本文档中充当运行示例。 本文档附带的示例代码是 Visual Studio 代码示例中弹性数据库工具示例的一部分。

## <a name="downloading-and-running-the-sample-code"></a>下载和运行示例代码

若要下载本文的代码：

* 需要 Visual Studio 2012 或更高版本。 
* 从 MSDN 下载 [Elastic DB Tools for Azure SQL - Entity Framework Integration sample](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba)（Azure SQL 弹性数据库工具 - Entity Framework 集成示例）。 将示例解压缩到所选位置。
* 启动 Visual Studio。 
* 在 Visual Studio 中，选择“文件”->“打开项目/解决方案”。 
* 在“打开项目”对话框中，导航到已下载的示例，并选择 **EntityFrameworkCodeFirst.sln** 打开该示例。 

要运行该示例，需要在 Azure SQL 数据库中创建三个空数据库：

* 分片映射管理器数据库
* 分片 1 数据库
* 分片 2 数据库

创建这些数据库后，使用 Azure SQL DB 服务器名称、数据库名称以及连接到数据库的凭据填充 Program.cs 中的占位符。 在 Visual Studio 中生成解决方案。 在生成过程中，Visual Studio 会下载弹性数据库客户端库、Entity Framework 和暂时性故障处理所需的 NuGet 包。 确保已为解决方案启用还原 NuGet 包。 可以通过右键单击 Visual Studio 解决方案资源管理器中的解决方案文件启用此设置。 

## <a name="entity-framework-workflows"></a>实体框架工作流

实体框架开发人员依靠以下四个工作流之一构建应用程序并确保应用程序对象的持久性：

* **Code First（新数据库）**：EF 开发人员在应用程序代码中创建模型，然后 EF 从中生成数据库。 
* **Code First（现有数据库）**：开发人员让 EF 从现有数据库生成模型的应用程序代码。
* **Model First**：开发人员在 EF 设计器中创建模型，EF 从该模型创建数据库。
* **Database First**：开发人员使用 EF 工具从现有数据库推断模型。 

所有这些方法依靠 DbContext 类为应用程序透明管理数据库连接和数据库架构。 DbContext 基类上的不同构造函数允许对连接创建、数据库引导和架构创建进行不同级别的控制。 挑战主要产生于这一事实：由 EF 提供的数据库连接管理与弹性数据库客户端库提供的数据依赖型路由接口的连接管理功能交叉。 

## <a name="elastic-database-tools-assumptions"></a>弹性数据库工具假设条件

有关术语定义，请参阅[弹性数据库工具词汇表](sql-database-elastic-scale-glossary.md)。

借助弹性数据库客户端库，可以定义称为 shardlet 的应用程序数据分区。 Shardlet 由分片键标识，并且映射到特定数据库。 应用程序可以具有任意所需数量的数据库，并根据当前业务需求分发 shardlet 以提供足够的容量或性能。 分片键值到数据库的映射由弹性数据库客户端 API 提供的分片映射存储。 此功能称为“分片映射管理”或简称为 SMM。 分片映射还为带有分片键的请求充当数据库连接的代理。 此功能称为“数据依赖型路由”。 

分片映射管理器防止用户在 shardlet 数据中出现不一致视图，当发生并发 shardlet 管理操作时（例如将数据从一个分片重新分配到另一个分片）可能发生此情况。 为此，客户端库管理的分片映射会代理应用程序的数据库连接。 当分片管理操作可能影响为其创建数据库连接的 shardlet 时，此操作允许分片映射功能自动终止该连接。 此方法需要与 EF 的一些功能集成，例如从现有连接创建新连接以检查数据库是否存在。 在通常情况下，我们观察到标准 DbContext 构造函数仅对可安全克隆用于 EF 工作的关闭数据库连接有效。 弹性数据库的设计原则是仅代理打开的连接。 有人可能认为，在交付给 EF DbContext 之前关闭由客户端库代理的连接可能解决此问题。 但是，通过关闭连接并依靠 EF 重新打开它，将放弃由该库执行的验证和一致性检查。 但是，EF 中的迁移功能使用这些连接以对应用程序透明的方式管理基础数据库架构。 理想情况下，将在相同的应用程序中保留和合并所有这些来自弹性数据库客户端库和 EF 的功能。 以下部分更详细地讨论这些属性和要求。 

## <a name="requirements"></a>要求

在使用弹性数据库客户端库和 Entity Framework API 时，会希望保留以下属性： 

* **向外缩放**：需要根据应用程序的容量需求，在分片应用程序的数据层中添加或删除数据库。 这意味着可以控制数据库的创建和删除，以及使用弹性数据库分片映射管理器 API 管理数据库和 shardlet 的映射。 
* **一致性**：应用程序利用分片，并且使用客户端库的数据依赖型路由功能。 若要避免损坏或错误的查询结果，连接通过分片映射管理器进行代理。 此操作还会保留验证和一致性。
* **Code First**：保留 EF 的 Code First 范例的便利性。 在“代码优先”中，应用程序中的类透明映射到基础数据库结构。 应用程序代码与 DbSet 交互以为基础数据库处理中涉及的大部分方面提供掩码。
* **架构**：实体框架通过迁移处理初始数据库架构创建和后续架构演变。 通过保留这些功能，随着数据的演变调整应用很容易。 

以下指南指导如何满足使用弹性数据库工具的“代码优先”应用程序的这些要求。 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>使用 EF DbContext 的数据依赖型路由

使用实体框架的数据库连接通常通过 **DbContext**的子类来管理。 通过从 **DbContext**派生创建这些子类。 这是定义 **DbSet** 的位置，它可为应用程序实现支持数据库的 CLR 对象的集合。 在数据依赖型路由的上下文中，可以标识多个有用的属性，这些属性不一定会为其他 EF Code First 应用程序方案保存： 

* 数据库已经存在，并且已在弹性数据库分片映射中注册。 
* 该应用程序的架构已部署到数据库（如下说明）。 
* 到数据库的数据相关路由连接由分片映射代理。 

将 **DbContexts** 与数据相关的路由集成以向外缩放：

1. 通过分片映射管理器的弹性数据库客户端接口创建物理数据库连接， 
2. 使用 **DbContext** 子类包装该连接。
3. 将该连接向下传递到 **DbContext** 基类以确保 EF 一侧上的所有处理也全部发生。 

以下代码示例演示了此方法。 （附带的 Visual Studio 项目中也提供此代码）

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection 
    // routed to the proper shard by the shard map manager. 
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the 
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap, 
    T shardingKey, 
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>要点

* 新的构造函数替换 DbContext 子类中的默认构造函数 
* 新的构造函数采用数据依赖型路由通过弹性数据库客户端库所需的参数：
  
  * 用于访问依赖于数据的路由接口的分片映射，
  * 用于标识 shardlet 的分片键，
  * 带有到该分片的依赖于数据的路由连接的凭据的连接字符串。 
* 对基类构造函数的调用需要绕行到静态方法，以执行依赖于数据的路由所需的所有步骤。 
  
  * 它使用分片映射上的弹性数据库客户端接口的 OpenConnectionForKey 调用来建立开放连接。
  * 分片映射创建到保存特定分片键的 shardlet 的分片的开放连接。
  * 此开放连接将传递回 DbContext 的基类构造函数以指示此连接由 EF 使用，而不是让 EF 自动创建新连接。 这样，该连接已由弹性数据库客户端 API 标记，以便它可以保证分片映射管理操作下的一致性。

为 DbContext 子类使用新的构造函数而不是代码中的默认构造函数。 以下是示例： 

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: "); 
var name = Console.ReadLine(); 

using (var db = new ElasticScaleContext<int>( 
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString)) 
{ 
    var blog = new Blog { Name = name }; 
    db.Blogs.Add(blog); 
    db.SaveChanges(); 

    // Display all Blogs for tenant 1 
    var query = from b in db.Blogs 
                orderby b.Name 
                select b; 
    ... 
}
```

新的构造函数将打开到该分片的连接，该分片保存由 **tenantid1** 的值标识的 shardlet 的数据。 **using** 块中的代码保持不变以访问 **DbSet**，进而获取有关对 **tenantid1** 的分片使用 EF 的博客。 这改变了 using 块中的代码的语义，因此所有数据库操作的范围现在设置为保留 **tenantid1** 的单个分片。 例如，博客 **DbSet** 上的 LINQ 查询将仅返回当前分片上存储的博客，不返回存储在其他分片上的博客。  

#### <a name="transient-faults-handling"></a>暂时性故障处理

Microsoft 模式和实践团队已发布[暂时性故障处理应用程序块](https://msdn.microsoft.com/library/dn440719.aspx)。 该库通过弹性缩放客户端库与 EF 结合使用。 但是，确保任何暂时性异常返回到可以确保新构造函数在暂时性故障后被使用的位置，以便任何新连接尝试使用微调过的构造函数来进行。 否则，不保证连接到正确分片，并且无法保证当分片映射发生更改时保持连接。 

以下代码示例演示如何围绕新的 **DbContext** 子类构造函数使用 SQL 重试策略： 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{ 
    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
        { 
                var blog = new Blog { Name = name }; 
                db.Blogs.Add(blog); 
                db.SaveChanges(); 
            ... 
        } 
    }); 
```

上述代码中的 **SqlDatabaseUtils.SqlRetryPolicy** 定义为 **SqlDatabaseTransientErrorDetectionStrategy**，重试计数为 10，每两次重试的等待时间为 5 秒。 此方法类似于 EF 和用户启动事务的指南（请参阅[重试执行策略的限制（从 EF6 开始）](https://msdn.microsoft.com/data/dn307226)。 这两种情况都要求应用程序控制返回暂时性异常的范围：重新打开事务，或者（如下所示）从使用弹性数据库客户端库的适当构造函数重新创建上下文。

需要控制其中暂时性异常返回范围还使该列不能使用 EF 随附的内置 **SqlAzureExecutionStrategy**。 **SqlAzureExecutionStrategy** 会重新打开连接，但不会使用 **OpenConnectionForKey**，从而绕过了调用 **OpenConnectionForKey** 期间执行的所有验证。 该代码示例使用的是 EF 也已随附的内置 **DefaultExecutionStrategy**。 与 **SqlAzureExecutionStrategy**相反，它能与暂时性故障处理中的重试策略正常配合工作。 执行策略在 **ElasticScaleDbConfiguration** 类中设置。 请注意，我们决定不使用 DefaultSqlExecutionStrategy，因为在发生暂时性异常时，最好使用 SqlAzureExecutionStrategy - 这会导致所述的错误行为。 有关不同重试策略和 EF 的详细信息，请参阅 [EF 中的连接弹性](https://msdn.microsoft.com/data/dn456835.aspx)。     

#### <a name="constructor-rewrites"></a>构造函数重写

上方的代码示例演示应用程序所需的默认构造函数重写，以将数据依赖型路由与 Entity Framework 一起使用。 下表将此方法一般化到其他构造函数。 

| 当前构造函数 | 为数据重写构造函数 | 基构造函数 | 注释 |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |该连接需要是分片映射和数据依赖路由键的一个函数。 需要通过 EF 绕过自动连接创建，并改用分片映射代理该连接。 |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |该连接是分片映射和依赖于数据的路由键的一个函数。 在它们通过分片映射绕过验证时，固定数据库名称或连接字符串将不起作用。 |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |会为给定分片映射和分片键创建连接，并提供模型。 编译后的模型会传递到基构造函数。 |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |该连接需要从分片映射和键推断。 无法将其作为输入提供（除非该输入已经在使用分片映射和键）。 会传递布尔模型。 |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |该连接需要从分片映射和键推断。 无法将其作为输入提供（除非该输入已在使用分片映射和键）。 会传递编译后的模型。 |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |新的构造函数需要确保 ObjectContext 中作为输入传递的任何连接重新路由到由 Elastic Scale 管理的连接。 ObjectContext 的更详细讨论不在本文档的范围内。 |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool)； |该连接需要从分片映射和键推断。 无法将连接作为输入提供（除非该输入已经在使用分片映射和键）。 模型和布尔值将传递到基类构造函数。 |

## <a name="shard-schema-deployment-through-ef-migrations"></a>通过 EF 迁移分片架构部署

自动架构管理是实体框架提供的一项便利。 在使用弹性数据库工具的应用程序的上下文中，会希望保留此功能以在数据库添加到分片应用程序时，将架构自动设置为新创建的分片。 主要用例是增加使用 EF 的分片应用程序的数据层的容量。 依靠 EF 的架构管理功能可减少在 EF 上构建的分片应用程序的数据库管理工作。 

通过 EF 迁移的架构部署对于 **未打开的连接**效果最佳。 这与依靠由弹性数据库客户端 API 提供的打开连接的数据依赖型路由方案相反。 另一个区别是一致性要求：尽管确保所有数据相关的路由连接的一致性以防止并发分片映射操作是可取的，但是对于到尚未在分片映射中注册以及尚未分配为保存 shardlet 的新数据库的初始架构部署，这不是问题。 因此，针对此方案，可以依靠常规数据库连接，与数据依赖型路由相反。  

这会导致一种方法，在此方法中通过 EF 迁移进行的架构部署将与新数据库的注册紧密耦合，充当应用程序的分片映射中的一个分片。 这依靠以下先决条件： 

* 该数据库已创建。 
* 该数据库为空 - 它未保存任何用户架构和用户数据。
* 该数据库无法通过数据相关的路由的弹性数据库客户端 API 访问。 

具备这些先决条件后，可以创建一个常规的未打开的 SqlConnection，以便为架构部署启动 EF 迁移。 以下代码示例演示了此方法。 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

public void RegisterNewShard(string server, string database, string connStr, int key) 
{ 

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
    connStrBldr.DataSource = server; 
    connStrBldr.InitialCatalog = database; 

    // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
    // This requires an un-opened connection. 
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
    { 
        // Run a query to engage EF migrations 
        (from b in db.Blogs 
            select b).Count(); 
    } 

    // Register the mapping of the tenant to the shard in the shard map. 
    // After this step, data-dependent routing on the shard map can be used 

    this.ShardMap.CreatePointMapping(key, shard); 
} 
```

此示例演示方法 **RegisterNewShard**，此方法注册分片映射中的分片，通过 EF 迁移部署架构并将分片键的映射存储到该分片。 它依靠 **DbContext** 子类的构造函数（在本示例中为 **ElasticScaleContext**），此构造函数采用 SQL 连接字符串作为输入。 此构造函数的代码很简单，如以下示例所示： 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

有人可能使用了从基类继承的构造函数版本。 但是该代码需要确保在连接时使用 EF 的默认初始化程序。 因此在调用带有连接字符串的基类构造函数前，需短暂绕行到静态方法。 请注意，分片的注册应该在不同的应用域或进程中运行，以确保 EF 的初始化程序设置不冲突。 

## <a name="limitations"></a>限制

本文档中概述的方法存在一些限制： 

* 使用 **LocalDb** 的 EF 应用程序在使用弹性数据库客户端库之前，需要先迁移到常规 SQL Server 数据库。 使用弹性缩放通过分片扩大应用程序不适用于 **LocalDb**。 请注意，开发仍然可以使用 **LocalDb**。 
* 任何意味着数据库架构更改的应用程序更改需要在所有分片上通过 EF 迁移。 本文档的示例代码不演示如何执行此操作。 考虑使用带有 ConnectionString 参数的 Update-Database 循环访问所有分片，或使用 Update-Database 与 –Script 选项提取用于挂起的迁移的 T-SQL 脚本，并将 T-SQL 脚本应用到分片。  
* 给定一个请求，假设它所有的数据库处理包含在单个分片内，如该请求所提供的分片键所标识的那样。 但是，此假设并不总是有效。 例如，当无法提供分片键时。 为解决此问题，客户端库提供 **MultiShardQuery** 类，此类可实现连接抽象以用于在多个分片上查询。 学习结合使用 **MultiShardQuery** 和 EF 不在本文档的范围内。

## <a name="conclusion"></a>结论

通过本文档中概述的步骤，EF 应用程序可以通过重构 EF 应用程序中使用的 DbContext 子类的构造函数来使用弹性数据库客户端库的数据依赖型路由功能。 这将所需的更改限制到 **DbContext** 类已经存在的位置。 此外，EF 应用程序可以通过将调用必要的 EF 迁移的步骤与新分片的注册和分片映射中的映射结合，继续从自动架构部署中受益。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png