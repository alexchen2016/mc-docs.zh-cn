---
title: 通过 Python 开始使用 Azure 表存储
description: 使用 Azure 表存储将结构化数据存储在云中。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
origin.date: 04/05/2018
ms.date: 03/04/2019
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 8cde465064d7e5a249f628773535b524b4e4f61d
ms.sourcegitcommit: b56dae931f7f590479bf1428b76187917c444bbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988049"
---
# <a name="get-started-with-azure-table-storage-using-python"></a>通过 Python 开始使用 Azure 表存储

<!-- Not Available on the Azure Cosmos DB Table API-->

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure 表存储是一项用于在云中存储结构化 NoSQL 数据的服务，通过无架构设计提供键/属性存储。 因为表存储无架构，因此可以很容易地随着应用程序需求的发展使数据适应存储。 对于许多类型的应用程序来说，访问表存储数据速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

### <a name="about-this-sample"></a>关于此示例
此示例介绍如何在常见的 Azure 表存储方案中使用[用于 Python 的 Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 该 SDK 的名称表示它适合与 Azure Cosmos DB 配合使用，但其实该 SDK 既适合与 Azure Cosmos DB 配合使用，也适合与 Azure 表存储配合使用，只不过每个服务具有唯一的终结点。 本文使用 Python 示例探索这些方案，以演示如何：
* 创建和删除表
* 插入和查询实体
* 修改实体

浏览此示例中的方案时，可能需要参考[用于 Python API 的 Azure Cosmos DB SDK 参考](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)。

## <a name="prerequisites"></a>先决条件

若要成功完成此示例，需要以下项：

- [Python](https://www.python.org/downloads/) 2.7、3.3、3.4、3.5 或 3.6
- [用于 Python 的 Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 此 SDK 连接到 Azure 表存储。
- [Azure 存储帐户](../storage/common/storage-quickstart-create-account.md)

<!-- Not Available on Azure Cosmos DB Table API -->
<!-- Not Avaiable on  [Azure Cosmos DB account](https://www.azure.cn/try/cosmosdb/) -->

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

<!--Not Avaiable on Cosmos DB Table API ### Create an Azure Cosmos DB Table API account -->

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>安装用于 Python 的 Azure Cosmos DB 表 SDK

创建存储帐户后，下一步是安装[用于 Python 的 Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 有关安装该 SDK 的详细信息，请参阅 GitHub 上用于 Python 的 Cosmos DB 表 SDK 存储库中的 [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) 文件。

## <a name="import-the-tableservice-and-entity-classes"></a>导入 TableService 和 Entity 类

若要通过 Python 使用 Azure 表服务中的实体，请使用 [TableService][py_TableService] 和 [Entity][py_Entity] 类。 将此代码添加到 Python 文件顶部附近，以便同时导入：

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>连接到 Azure 表服务

若要连接到 Azure 存储表服务，请创建 [TableService][py_TableService] 对象，并传入存储帐户名称和帐户密钥。 将 `myaccount` 和 `mykey` 替换为自己的帐户名和密钥。

```python
table_service = TableService(account_name='myaccount', account_key='mykey',endpoint_suffix='core.chinacloudapi.cn')
```
<!--MOONCAKE: Add the endpoint_suffix configuration -->
<!-- Not Avaiable on ## Connect to Azure Cosmos DB -->

## <a name="create-a-table"></a>创建表

调用 [create_table][py_create_table] 创建表。

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表

若要添加实体，请先创建一个表示实体的对象，然后将该对象传递给 [TableService.insert_entity 方法][py_TableService]。 实体对象可以是一个[实体][py_Entity]类型的字典和对象，且可定义实体的属性名和值。 除了为实体定义的任何其他属性外，每个实体还必须包含 [PartitionKey 和 RowKey](#partitionkey-and-rowkey) 属性。

此示例将创建一个表示实体的字典对象，并将该对象传递给 [insert_entity][py_insert_entity] 方法以将其添加到表：

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

此示例将创建一个[实体][py_Entity]对象，并将该对象传递给 [insert_entity][py_insert_entity] 方法以将其添加到表：

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey 和 RowKey

必须为每个实体同时指定 PartitionKey 和 RowKey 属性。 这些是实体的唯一标识符，它们一起构成了实体的主键。 由于只对这些属性编制了索引，因此可使用这些值进行查询，速度比查询任何其他实体属性都要快。

表服务使用 PartitionKey 在存储节点中智能分发。 具有相同的 PartitionKey 的实体存储在同一个节点上。 RowKey 是实体在其所属分区内的唯一 ID。

## <a name="update-an-entity"></a>更新条目

若要更新实体的所有属性值，请调用 [update_entity][py_update_entity] 方法。 此示例演示如何使用更新版本替换现有实体：

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

如果要更新的实体不存在，更新操作将失败。 如果要存储实体，无论它是否已存在，请使用 [insert_or_replace_entity][py_insert_or_replace_entity]。 在下面的示例中，第一次调用会替换现有实体。 第二个调用将插入新实体，因为表中不存在具有指定 PartitionKey 和 RowKey 的实体。

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [update_entity][py_update_entity] 方法将替换现有实体的所有属性和值，还可将其用于从现有实体中删除属性。 可以使用 [merge_entity][py_merge_entity] 方法更新含新的或修改后的属性值的现有实体，而无需彻底替换该实体。

## <a name="modify-multiple-entities"></a>修改多个实体

若要确保通过表服务进行原子处理，可以批量同时提交多个操作。 首先，使用 [TableBatch][py_TableBatch] 类将多个操作添加到单个批处理中。 接下来，调用 [TableService][py_TableService].[commit_batch][py_commit_batch] 以原子操作的形式提交操作。 批处理中要修改的实体必须位于同一分区。

该示例将两个实体一起添加到批处理中：

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

还可以通过上下文管理器语法来使用批处理：

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>查询实体

要查询表中的实体，请将 PartitionKey 和 RowKey 传递给 [TableService][py_TableService].[get_entity][py_get_entity] 方法。

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>查询一组条目

在筛选器字符串中提供 filter 参数，可以查询一组实体。 此示例通过对 PartitionKey 应用筛选器来查找 Seattle 中的所有任务：

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性

还可以限制查询中每个实体所返回的属性。 此方法称为“投影”，可减少带宽并提高查询性能，尤其适用于大型实体或结果集。 使用 select 参数并传递希望返回给客户端的属性的名称。

以下代码中的查询只返回表中实体的说明。

> [!NOTE]
> 下面的代码段仅对 Azure 存储有效。 但不受存储模拟器支持。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>删除条目

将实体的 **PartitionKey** 和 **RowKey** 传递给 [delete_entity][py_delete_entity] 方法，以删除该实体。

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>删除表

如果不再需要表或表中的所有实体，请调用 [delete_table][py_delete_table]方法，从 Azure 存储永久删除该表。

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>后续步骤

<!-- Not Available on  [FAQ - Develop with the Table API](/cosmos-db/faq#develop-with-the-table-api)-->

* [用于 Python API 的 Azure Cosmos DB SDK 参考](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Python 开发人员中心](/develop/python/)
* [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)：一种跨平台的免费应用程序，用于在 Windows、macOS 和 Linux 上对 Azure 存储数据进行可视化处理。
* [在 Visual Studio (Windows) 中使用 Python](https://docs.microsoft.com/zh-cn/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python

<!--Update_Description: update meta propreties, wording update, update link-->