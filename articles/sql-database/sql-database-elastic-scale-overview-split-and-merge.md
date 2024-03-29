---
title: 在扩展云数据库之间移动数据 | Microsoft Docs
description: 介绍如何使用弹性数据库 API 通过自托管服务来操作分片和移动数据。
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
origin.date: 03/12/2019
ms.date: 04/08/2019
ms.openlocfilehash: 57e97b109ff114ead1d83ffd190ad6386f02407a
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003497"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>在扩大云数据库之间移动数据

如果是软件即服务开发人员，并且应用突然遇到巨大需求，那么需要适应该需求增长。 因此，你添加了更多数据库（分片）。 如何在不破坏数据完整性的情况下将数据重新分配到新数据库？ 使用**拆分/合并工具**将数据从受约束的数据库移到新数据库。  

将拆分/合并工具作为 Azure web 服务运行。 管理员或开发人员使用该工具在不同数据库 （分片）之间移动 shardlet（一个分片中的数据）。 该工具使用分片映射管理来维护服务元数据数据库，并确保一致的映射。

![概述][1]

## <a name="download"></a>下载

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>文档

1. [弹性数据库拆分/合并工具教程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [拆分/合并安全配置](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [拆分/合并安全注意事项](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [分片映射管理](sql-database-elastic-scale-shard-map-management.md)
5. [迁移要扩展的现有数据库](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [弹性数据库工具](sql-database-elastic-scale-introduction.md)
7. [弹性数据库工具词汇表](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>为什么使用拆分/合并工具

- **灵活性**

  应用程序需要灵活伸展到超出单个 Azure SQL DB 数据库的限制。 根据需要使用该工具将数据移到新的数据库，同时保留完整性。

- **通过拆分实现增长**

  为了提高处理爆炸性增长的总体容量，需要通过对数据进行分片并将数据分发给越来越多的数据库来提供额外容量，直到满足容量需求。 这是“拆分”功能的一个典型示例。

- **通过合并实现缩减**

  由于业务的季节性，需要缩减容量。 当业务减少时，使用该工具可减少到更少的缩放单元。 弹性缩放拆分/合并服务的“合并”功能可以满足此要求。

- **通过移动 Shardlet 管理热点**

  在一个数据库中具有多个租户情况下，将 shardlet 分配到分片可能导致某些分片上出现容量瓶颈。 这就要求重新分配 shardlet 或将工作中的 shardlet 移动到新的或容量使用较少的分片上。

## <a name="concepts--key-features"></a>概念和主要功能

- **客户托管服务**

  拆分/合并将作为客户托管的服务交付。 必须在 Azure 订阅中部署和托管该服务。 从 NuGet 下载的程序包将包含一个要使用特定部署信息完成的配置模板。 有关详细信息，请参阅[拆分/合并教程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。 由于服务在 Azure 订阅中运行，因此可以控制和配置该服务的大多数安全设置。 默认模板包括配置 SSL 的选项、基于证书的客户端身份验证、存储凭据的加密、DoS 防护和 IP 限制。 你可以在以下[拆分/合并安全配置](sql-database-elastic-scale-split-merge-security-configuration.md)文档中找到有关安全方面的详细信息。

  默认部署的服务可与一个辅助角色和一个 Web 角色同时运行。 在 Azure 云服务中，每个角色都使用 A1 VM 大小。 虽然你无法在部署程序包时修改这些设置，但是可以在运行的云服务中成功进行部署之后更改它们（通过 Azure 门户）。 请注意，出于技术方面的原因，不得为多个实例配置辅助角色。

- **分片映射集成**

  拆分/合并服务可与应用程序的分片映射进行交互。 使用拆分/合并服务拆分或合并范围或者在分片之间移动 shardlet 时，该服务会使分片映射自动保持最新。 为实现此目的，该服务将连接到应用程序的分片映射管理器数据库并将这些范围和映射保留为拆分/合并/移动请求进度。 这可确保在进行拆分/合并操作时，分片映射始终显示最新视图。 通过将一批 shardlet 从源分片移动到目标分片来实现拆分、合并和 shardlet 移动操作。 在 shardlet 移动操作过程中，属于当前批的 shardlet 在分片映射中标记为脱机，并且不可用于使用 **OpenConnectionForKey** API 进行依赖于数据的路由连接。

- **一致的 shardlet 连接**

  为了避免不一致，当一批新的 shardlet 开始进行数据移动时，将断开到存储 shardlet 的分片的所有分片映射提供的数据依赖型路由连接；当数据移动正在进行时，将阻止从分片映射 API 到这些 shardlet 的后续连接。 到同一分片上其他 shardlet 的连接也会断开，但是重试时会立即再次成功连接。 移动此批后，目标分片的 shardlet 将再次被标记为联机状态，并且源数据将从源分片中删除。 该服务将针对每一批执行以上步骤，直到所有 shardlet 都已移动。 在完成拆分/合并/移动操作过程中，这会导致几个连接中断操作。  

- **管理 shardlet 可用性**

  如上所述对当前批的 shardlet 的连接中断进行限制，从而限制一次到一批 shardlet 的不可用范围。 与在拆分或合并操作过程中，整个分片针对其所有 shardlet 保持脱机的方法相比，此方法是首选方法。 定义为一次要移动的不同 shardlet 数的批大小是一个配置参数。 可以根据应用程序的可用性和性能需求为每个拆分与合并操作定义该配置参数。 请注意，将在分片映射中锁定的范围可能会大于指定的批大小。 这是因为服务会选取范围大小，以使数据中分片键值的实际数目大约等于批大小。 这对于稀少的分片键而言尤为重要。

- **元数据存储**

  拆分/合并服务使用数据库来维护其状态，并在请求处理期间保存日志。 用户在其订阅中创建此数据库并在服务部署的配置文件中为其提供连接字符串。 来自用户所在组织的管理员也可以连接到此数据库，以查看请求进度和调查有关潜在故障的详细信息。

- **分片感知**

  可从以下方面区分拆分和合并服务：（1）分片表、（2）引用表和（3）普通表。 拆分/合并/移动操作的语义取决于所用表的类型，定义如下：

  - **分片表**

    拆分、合并与移动操作将 shardlet 从源分片移动到目标分片。 成功完成整个请求后，这些 shardlet 不会再显示在源分片上。 请注意，目标表需要存在于目标分片上，并且在处理操作之前，目标表中不能包含目标范围中的数据。

  - **引用表**

    对于引用表，拆分、合并和移动操作会将数据从源分片复制到目标分片。 但是，请注意，如果目标分片上的给定表中已存在任何行，则该表在目标分片上不会发生任何更改。 要使任何引用表复制操作得到处理，该表必须为空。

  - **其他表**

    其他表可存在于拆分与合并操作的源或目标上。 对于任何数据移动或复制操作，拆分/合并服务都会忽略这些表。 但是，请注意，在出现约束的情况下，它们可能会干扰这些操作。

    有关引用表和分片表对比的信息可由分片映射上的 `SchemaInfo` API 提供。 以下示例说明了如何在给定分片映射管理器对象上使用这些 API：

    ```c#
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // Reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // Sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));
    // Publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    将表“region”和表“nation”定义为引用表，并使用拆分/合并/移动操作复制它们， 而将“customer”和“orders”定义为分片表。 `C_CUSTKEY` 和 `O_CUSTKEY` 用作分片键。 

- **引用完整性**

  拆分/合并服务会分析各表之间的依赖关系，并使用外键-主键关系来暂存用于移动引用表和 shardlet 的操作。 通常，首先按依赖项顺序复制引用表，并按每一批中 shardlet 的依赖项顺序复制 shardlet。 这是必要的，以便在新的数据到达时遵循目标分片上的外键-主键约束。

- **分片映射一致性和最终完成**

  出现故障时，拆分/合并服务会在发生任何中断后恢复操作，旨在完成任何正在进行的请求。 但是，也可能存在未涉及到的情况，例如，目标分片丢失或泄露，无法修复。 在这些情况下，一些本应移动的 shardlet 可能会继续驻留在源分片上。 该服务可确保仅在已将必需的数据成功地复制到目标分片后才更新 shardlet 映射。 仅当已将 shardlet 的所有数据都成功地复制到目标分片并已成功地更新对应的映射后，才在源分片上删除 shardlet。 当目标分片上的范围已处于联机状态时，删除操作会在后台执行。 拆分/合并服务始终确保存储在分片映射中的映射的正确性。

## <a name="the-split-merge-user-interface"></a>拆分/合并用户界面

拆分/合并 Service Pack 包含辅助角色和 Web 角色。 Web 角色用于以交互方式提交拆分/合并请求。 用户界面的主要组件如下：

- **操作类型**

  操作类型是一个单选按钮，用于控制针对此请求由服务执行的操作类型。 可以在拆分、合并和移动方案之间选择。 还可以取消以前提交的操作。 可以使用拆分、合并和移动请求来设置分片映射的范围。 列表分片映射仅支持移动操作。

- **分片映射**

  请求参数的下一部分包含有关分片映射和托管分片映射的数据库的信息。 具体而言，需要提供托管分片映射的 Azure SQL 数据库服务器和数据库的名称、用于连接到分片映射数据库的凭据以及分片映射的名称。 当前，该操作仅接受一个凭据集。 这些凭据需要具有足够的权限，才能对分片映射和分片上的用户数据执行更改。

- **源范围（拆分与合并）**

  拆分与合并操作将使用范围的低键和高键来处理该范围。 若要使用无边界的高键值指定操作，请选中“高键为最大值”复选框，并将高键字段留空。 指定的范围键值不需要与分片映射中的映射及其边界精确匹配。 如果未指定任何范围边界，服务会自动推断最接近的范围。 可以使用 GetMappings.ps1 PowerShell 脚本检索给定分片映射中的当前映射。

- **拆分源行为（拆分）**

  对于拆分操作，请定义要拆分源范围的点。 可通过提供希望进行拆分的分片键来完成此操作。 使用单选按钮指定是否要移动范围的下半部分（不包括拆分键），或是否要移动上半部分（包括拆分键）。

- **源 Shardlet（移动）**

  移动操作不同于拆分操作或合并操作，因为它们不需要范围来描述源。 用于移动的源仅由你计划移动的分片键值标识。

- **目标分片（拆分）**

  提供了有关拆分操作的源的信息后，需要通过提供目标的 Azure SQL DB 服务器和数据库名称来定义数据复制的目标位置。

- **目标范围（合并）**

  合并操作会将 shardlet 移动到现有分片上。 可以通过提供希望合并的现有范围的范围边界来标识现有分片。

- **批大小**

  批大小控制在数据移动过程中将处于脱机状态的 shardlet 数。 这是一个整数值，对 shardlet 的长期停机时间敏感时，可以使用其中的较小值。 较大值会增加给定 shardlet 处于脱机状态的时间，但是可能会提高性能。

- **操作 ID（取消）**

  如果具有不再需要的进行中的操作，则可以通过在此字段中提供其操作 ID 来取消该操作。 可以从请求状态表（请参阅章节 8.1）或提交请求的 Web 浏览器的输出中检索该操作 ID。

## <a name="requirements-and-limitations"></a>要求和限制

拆分/合并服务的当前实现遵循以下要求和限制：

- 必须存在分片并且这些分片已在分片映射中注册，才可以对这些分片执行拆分/合并操作。
- 该服务未将表或任何其他数据库对象的自动创建作为其操作的一部分。 这意味着在任何拆分/合并/移动操作之前，所有分片表和引用表的架构都需要存在于目标分片上。 在要通过拆分/合并/移动操作添加新的 shardlet 的范围中，尤其要求分片表为空。 否则，该操作将无法通过目标分片上的初始一致性检查。 此外，请注意，仅当引用表为空时才复制引用数据，而且对于引用表上的其他并发写入操作没有一致性保证。 我们建议：在运行拆分/合并操作的同时不要使其他写入操作对引用表做出更改。
- 该服务依赖于行标识（由包含分片键的唯一索引或键构建）来提高较大 shardlet 的性能和可靠性。 这使该服务能够移动粒度比分片键值更加精细的数据。 这有助于减少操作过程中必需的日志空间和锁定的最大数量。 如果希望通过拆分/合并/移动请求使用给定表，请考虑在该表上创建一个包括分片键的唯一索引或主键。 出于性能原因，分片键应为键或索引中的起始列。
- 在请求处理过程中，一些 shardlet 数据可能会同时存在于源分片和目标分片上。 为了防止在 shardlet 移动过程中出现故障，这是必需的。 拆分/合并服务与分片映射功能的集成可以确保在分片映射上使用“OpenConnectionForKey”方法通过依赖于数据的路由 API 建立的连接不会显示任何不一致的中间状态。 但是，在不使用 **OpenConnectionForKey** 方法连接到源分片或目标分片时，如果正在执行拆分/合并/移动请求，则不一致的中间状态可能可见。 这些连接可能会显示部分或重复的结果，具体取决于时间设置或进行基础连接的分片。 此限制当前包括由弹性缩放多分片查询建立的连接。
- 不能在不同的角色之间共享用于拆分/合并服务的元数据数据库。 例如，在过渡环境中运行的拆分/合并服务的角色需要指向其他元数据数据库而不是生产角色。

## <a name="billing"></a>计费

在 Azure 订阅中拆分/合并服务作为云服务运行。 因此会对你的服务实例收取云服务费用。 除非频繁地执行拆分/合并/移动操作，否则建议删除拆分/合并云服务。 这可以节省用于运行中的或已部署的云服务实例的成本。 只要需要执行拆分或合并操作，便可以重新部署和启用已准备好的可运行配置。 

## <a name="monitoring"></a>监视

### <a name="status-tables"></a>状态表

拆分/合并服务在元数据存储数据库中提供用于监视已完成和正在进行的请求的 **RequestStatus** 表。 该表为已提交到拆分/合并服务的此实例的每个拆分/合并请求列出一行。 它为每个请求提供以下信息：

- **Timestamp**

  发起请求时的时间和日期。

- **OperationId**

  唯一标识请求的 GUID。 此请求也可用于取消仍在进行的操作。

- **状态**

  该请求的当前状态。 对于正在进行的请求，它还会列出请求所在的当前阶段。

- **CancelRequest**

  用于指示是否已取消请求的标志。

- **进度**

  该操作完成过程的百分比估计。 值 50 指示该操作大约完成 50%。

- **详细信息**

  用于提供更详细的进度报告的 XML 值。 当将多组行从源复制到目标时，进度报告会定期更新。 此列还包括有关故障的详细信息，以防故障或异常。

### <a name="azure-diagnostics"></a>Azure 诊断

拆分/合并服务使用基于 Azure SDK 2.5 的 Azure Diagnostics 进行监视与诊断。 可以按照此处的说明控制诊断配置：[在 Azure 云服务和虚拟机中启用诊断](../cloud-services/cloud-services-dotnet-diagnostics.md) 下载包包含两个诊断配置 - 一个用于 Web 角色，另一个用于辅助角色。 它包括用于记录性能计数器、IIS 日志、Windows 事件日志和拆分/合并应用程序事件日志的定义。

## <a name="deploy-diagnostics"></a>部署诊断

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

针对 NuGet 包所提供的 Web 和辅助角色，若要使用诊断配置启用监视和诊断，请使用 Azure PowerShell 运行以下命令：

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

可以在此处找到有关如何配置和部署诊断设置的详细信息：[在 Azure 云服务和虚拟机中启用诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)

## <a name="retrieve-diagnostics"></a>检索诊断

可以从服务器资源管理器树的 Azure 部分中的 Visual Studio 服务器资源管理器轻松访问诊断。 打开 Visual Studio 实例，并在菜单栏中，依次单击“视图”和“服务器资源管理器”。 单击 Azure 图标连接到 Azure 订阅。 然后，导航到“Azure”->“存储”->“`<your storage account>`”->“表”->“WADLogsTable”。 有关详细信息，请参阅[服务器资源管理器](https://msdn.microsoft.com/library/x603htbk.aspx)。

![WADLogsTable][2]

上图中突出显示的 WADLogsTable 包含来自拆分/合并服务的应用程序日志的详细事件。 请注意，已下载包提供的默认配置面向生产部署。 因此，从服务实例中提取日志和计数器的时间间隔较大（5 分钟）。 对于测试和开发，可以通过按需调整 Web 或辅助角色的诊断设置来减少该时间间隔。 右键单击 Visual Studio 服务器资源管理器中的角色（如上所示），然后在对话框中调整诊断配置设置的传输时间段： 

![配置][3]

## <a name="performance"></a>性能

通常，Azure SQL 数据库中更高、更可执行的服务层应具有更好的性能。 为更高服务层分配更高的 IO、CPU 和内存有利于拆分/合并服务使用的批量复制和删除操作。 因此，在定义的有限时间段内仅为这些数据库提高服务层。

该服务也会将验证查询作为其常规操作的一部分来执行。 这些验证查询还会检查目标范围中数据的异常存在，确保任何拆分/合并/移动操作都从一致状态开始进行。 这些查询在操作范围定义的分片键范围和作为请求定义的一部分而提供的批大小上都有效。 当使用分片键作为起始列的索引存在时，这些查询表现最好。

此外，使用分片键作为起始列的唯一性将使服务能够使用一种优化的方式来限制日志空间和内存方面的资源使用。 若要移动较大数据大小（通常为 1GB 以上），此唯一性是必需的。

## <a name="how-to-upgrade"></a>如何升级

1. 请按照[部署拆分/合并服务](sql-database-elastic-scale-configure-deploy-split-and-merge.md)中的步骤进行操作。
2. 更改拆分/合并部署的云服务配置文件，以反映新的配置参数。 新的必需参数是用于加密的证书的相关信息。 执行此操作的简单方法是将下载的新配置模板文件与现有配置进行比较。 请确保添加 Web 和辅助角色的“DataEncryptionPrimaryCertificateThumbprint”与“DataEncryptionPrimary”设置。
3. 将更新部署到 Azure 之前，请确保当前运行的所有拆分/合并操作都已完成。 做法很简单，可以针对进行中的请求，查询拆分/合并元数据数据库中的 RequestStatus 和 PendingWorkflows 表。
4. 使用新程序包和更新的服务配置文件，在 Azure 订阅中更新拆分/合并的现有云服务部署。

无需预配新的元数据数据库，即可升级拆分/合并。 新版本会自动将现有的元数据数据库升级到新版本。

## <a name="best-practices--troubleshooting"></a>最佳做法和疑难解答

- 定义一个测试租户，并使用该测试租户在几个分片上对最重要的拆分/合并/移动操作进行实验。 确保已在分片映射中正确定义所有元数据，并且这些操作不违反约束或外键。
- 请使测试租户的数据大小始终大于最大租户的最大数据大小，以确保不会遇到与数据大小有关的问题。 这会帮助你评估移动单个租户所需时间的上限。
- 确保架构允许删除操作。 拆分/合并服务要求在将数据成功地复制到目标分片后，能够从源分片中删除数据。 例如， **删除触发器** 可以阻止该服务删除源分片上的数据，并且可能导致操作失败。
- 分片键在主键或唯一索引定义中应该是起始列。 这可以确保拆分或合并验证查询以及实际的数据移动和删除操作（始终在分片键范围上执行）的最佳性能。
- 将拆分/合并服务共置于数据库所在的区域和数据中心。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
