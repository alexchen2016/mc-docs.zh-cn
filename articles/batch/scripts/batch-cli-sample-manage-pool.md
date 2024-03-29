---
title: Azure CLI 脚本示例 - 管理批处理中的池 | Microsoft Docs
description: Azure CLI 脚本示例 - 管理批处理中的池
services: batch
documentationcenter: ''
author: alexchen2016
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
origin.date: 05/02/2017
ms.date: 07/04/2017
ms.author: v-junlch
ms.openlocfilehash: 39b5f57b724b0aa703441bd70c407b683d7297d7
ms.sourcegitcommit: c43ca3018ef00245a94b9a7eb0901603f62de639
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56987014"
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>使用 Azure CLI 管理 Azure Batch 池

这些脚本演示了 Azure CLI 中一些可用于在 Azure Batch 服务中创建和管理计算节点池的工具。

> [!NOTE]
> 本示例中的命令创建 Azure 虚拟机。 运行中的 VM 会在帐户中产生费用。 若要尽可能减少这些费用，请在运行完本示例后删除 VM。 请参阅[清理池](#clean-up-pools)。

可通过两种方式配置批处理池：使用云服务配置（仅适用于 Windows），或使用虚拟机配置（适用于 Windows 和 Linux）。 以下示例脚本显示如何使用这两个配置创建池。

## <a name="prerequisites"></a>先决条件

- 按照 [Azure CLI 安装指南](/cli/install-azure-cli)中提供的说明安装 Azure CLI（如果尚未这样做）。
- 创建 Batch 帐户（如果还没有帐户）。 有关创建帐户的示例脚本，请参阅[使用 Azure CLI 创建 Batch 帐户](/batch/scripts/batch-cli-sample-create-account/)。
- 将应用程序配置为从启动任务运行（如果尚未这样做）。 有关用于创建应用程序并将应用程序包上传到 Azure 的示例脚本，请参阅[使用 Azure CLI 将应用程序添加到 Azure Batch](/batch/scripts/batch-cli-sample-add-application/)。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>使用云服务配置来配置池的示例脚本
```azurecli
#!/bin/bash

# Authenticate Batch account CLI session.
az batch account login -g myresourcegroup -n mybatchaccount

# We want to add an application package reference to the pool, so first
# we'll list the available applications.
az batch application summary list

# Create a new Windows cloud service platform pool with 3 Standard A1 VMs.
# The pool has an application package reference (taken from the output of the
# above command) and a start task that will copy the application files to a shared directory.
az batch pool create \
    --id mypool-windows \
    --os-family 4 \
    --target-dedicated 3 \
    --vm-size small \
    --start-task-command-line "cmd /c xcopy %AZ_BATCH_APP_PACKAGE_MYAPP% %AZ_BATCH_NODE_SHARED_DIR%" \
    --start-task-wait-for-success \
    --application-package-references myapp

# We can add some metadata to the pool.
az batch pool set --pool-id mypool-windows --metadata IsWindows=true VMSize=StandardA1

# Let's change the pool to enable automatic scaling of compute nodes.
# This autoscale formula specifies that the number of nodes should be adjusted according
# to the number of active tasks, up to a maximum of 10 compute nodes.
az batch pool autoscale enable \
    --pool-id mypool-windows \
    --auto-scale-formula "$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));$TargetDedicated = min(10, $averageActiveTaskCount);"

# We can monitor the resizing of the pool.
az batch pool show --pool-id mypool-windows

# Once we no longer require the pool to automatically scale, we can disable it.
az batch pool autoscale disable --pool-id mypool-windows
```

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>使用虚拟机配置来配置池的示例脚本
```azurecli
#!/bin/bash

# Authenticate Batch account CLI session.
az batch account login -g myresourcegroup -n mybatchaccount

# Retrieve a list of available images and node agent SKUs.
az batch pool node-agent-skus list

# Create a new Linux pool with a virtual machine configuration. The image reference 
# and node agent SKUs ID can be selected from the ouptputs of the above list command.
# The image reference is in the format: {publisher}:{offer}:{sku}:{version} where {version} is
# optional and will default to 'latest'.
az batch pool create \
    --id mypool-linux \
    --vm-size Standard_A1 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id batch.node.ubuntu 16.04

# Now let's resize the pool to start up some VMs.
az batch pool resize --pool-id mypool-linux --target-dedicated 5

# We can check the status of the pool to see when it has finished resizing.
az batch pool show --pool-id mypool-linux

# List the compute nodes running in a pool.
az batch node list --pool-id mypool-linux

# If a particular node in the pool is having issues, it can be rebooted or reimaged.
# The ID of the node can be retrieved with the list command above.
# A typical node ID will be in the format 'tvm-xxxxxxxxxx_1-<timestamp>'.
az batch node reboot --pool-id mypool-linux --node-id tvm-123_1-20170316t000000z

# Alternatively, one or more compute nodes can be deleted from the pool, and any
# work already assigned to it can be re-allocated to another node.
az batch node delete \
    --pool-id mypool-linux \
    --node-list tvm-123_1-20170316t000000z tvm-123_2-20170316t000000z \
    --node-deallocation-option requeue
```

## <a name="clean-up-pools"></a>清理池

运行上述示例脚本后，可运行以下命令删除池。
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建和操作批处理池。
表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az batch account login](/cli/batch/account#login) | 针对批处理帐户进行身份验证。  |
| [az batch application summary list](/cli/batch/application/summary#list) | 列出批处理帐户中可用的应用程序。  |
| [az batch pool create](/cli/batch/pool#create) | 创建 VM 池。  |
| [az batch pool set](/cli/batch/pool#set) | 更新池的属性。  |
| [az batch pool node-agent-skus list](/cli/batch/pool/node-agent-skus#list) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool resize](/cli/batch/pool#resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](/cli/batch/pool#show) | 显示池的属性。  |
| [az batch pool delete](/cli/batch/pool#delete) | 删除指定的池。  |
| [az batch pool autoscale enable](/cli/batch/pool/autoscale#enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool autoscale disable](/cli/batch/pool/autoscale#disable) | 对池禁用自动缩放。  |
| [az batch node list](/cli/batch/node#list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](/cli/batch/node#reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](/cli/batch/node#delete) | 从指定的池中删除列出的节点。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview)。


