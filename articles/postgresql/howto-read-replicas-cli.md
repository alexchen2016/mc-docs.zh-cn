---
title: 通过 Azure CLI 管理 Azure Database for PostgreSQL（单一服务器）的只读副本
description: 了解如何通过 Azure CLI 管理 Azure Database for PostgreSQL（单一服务器）中的只读副本。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 5/6/2019
ms.date: 05/20/2019
ms.openlocfilehash: 4614f04f3cabd8354eea2f145d2496d2da977551
ms.sourcegitcommit: 11d81f0e4350a72d296e5664c2e5dc7e5f350926
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65732010"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>在 Azure CLI 中创建和管理只读副本

本文介绍如何使用 Azure CLI 在 Azure Database for PostgreSQL 中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

> [!NOTE]
> Azure CLI 尚不支持在与主服务器不同的区域中创建副本。 若要创建跨区域副本，请使用 [Azure 门户](howto-read-replicas-portal.md)。

## <a name="prerequisites"></a>先决条件
- 用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。

本文要求运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/install-azure-cli)。 


## <a name="prepare-the-master-server"></a>准备主服务器
必须使用以下步骤在“常规用途”和“内存优化”层中准备主服务器。

主服务器上的 `azure.replication_support` 参数必须设置为 **REPLICA**。 更改此静态参数后，需要重启服务器才能使更改生效。

1. 将 `azure.replication_support` 设置为 REPLICA。

   ```azurecli
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 重启即可应用对服务器所做的更改。

   ```azurecli
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>创建只读副本

[az postgres server replica create](/cli/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  要在其中创建副本服务器的资源组。  |
| name | mydemoserver-replica | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有主服务器的名称或资源 ID。 |

```azurecli
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

如果尚未在“常规用途”或“内存优化”主服务器上将 `azure.replication_support` 参数设置为 **REPLICA** 并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

副本是使用与主服务器相同的服务器配置创建的。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与主服务器发生的任何更改保持同步。

## <a name="list-replicas"></a>列出副本
可以使用 [az postgres server replica list](/cli/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) 命令查看主服务器的副本的列表。

```azurecli
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用 [az postgres server replica stop](/cli/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) 命令停止在主服务器和只读副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```azurecli
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用 [az postgres server delete](/cli/postgres/server?view=azure-cli-latest#az-postgres-server-delete) 命令。

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```azurecli
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤
详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。