---
title: Azure CLI 脚本示例 - 为应用创建计划备份 | Azure Docs
description: Azure CLI 脚本示例 - 为应用创建计划备份
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
origin.date: 12/11/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: 136d5528b61c20bdb59afcbf13c891c422b2449e
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083928"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>使用 CLI 为应用服务应用创建计划备份

此示例脚本使用其相关资源，在应用服务中创建应用，然后为其创建计划备份。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-lastest)。 

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

groupname="myResourceGroup"
planname="myAppServicePlan"
webappname=mywebapp$RANDOM
storagename=mywebappstorage$RANDOM
location="chinaeast"
container="appbackup"
expirydate=$(date -I -d "$(date) + 1 month")

# Create a Resource Group 
az group create --name $groupname --location $location

# Create a Storage Account
az storage account create --name $storagename --resource-group $groupname --location $location \
--sku Standard_LRS

# Create a storage container
az storage container create --account-name $storagename --name $container

# Generates an SAS token for the storage container, valid for one month.
# NOTE: You can use the same SAS token to make backups in App Service until --expiry
sastoken=$(az storage container generate-sas --account-name $storagename --name $container \
--expiry $expirydate --permissions rwdl --output tsv)

# Construct the SAS URL for the container
sasurl=https://$storagename.blob.core.chinacloudapi.cn/$container?$sastoken

# Create an App Service plan in Standard tier. Standard tier allows one backup per day.
az appservice plan create --name $planname --resource-group $groupname --location $location \
--sku S1

# Create a web app
az webapp create --name $webappname --plan $planname --resource-group $groupname

# Schedule a backup every day and retain for 10 days
az webapp config backup update --resource-group $groupname --webapp-name $webappname \
--container-url $sasurl --frequency 1d --retain-one true --retention 10

# Show the current scheduled backup configuration
az webapp config backup show --resource-group $groupname --webapp-name $webappname

# List statuses of all backups that are complete or currently executing
az webapp config backup list --resource-group $groupname --webapp-name $webappname

# (OPTIONAL) Change the backup schedule to every 2 days
az webapp config backup update --resource-group $groupname --webapp-name $webappname \
--container-url $sasurl --frequency 2d --retain-one true --retention 10
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az storage account create`](https://docs.azure.cn/zh-cn/cli/storage/account?view=azure-cli-latest#az_storage_account_create) | 创建存储帐户。 |
| [`az storage container create`](https://docs.azure.cn/zh-cn/cli/storage/container?view=azure-cli-latest#az_storage_container_create) | 创建 Azure 存储容器。 |
| [`az storage container generate-sas`](https://docs.azure.cn/zh-cn/cli/storage/container?view=azure-cli-latest#az_storage_container_generate_sas) | 为 Azure 存储容器生成 SAS 令牌。  |
| [`az appservice plan create`](https://docs.azure.cn/zh-cn/cli/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/zh-cn/cli/webapp?view=azure-cli-latest#az_webapp_create) | 创建 Azure Web 应用。 |
| [`az webapp config backup update`](https://docs.azure.cn/zh-cn/cli/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_update) | 为 Web 应用配置新的备份计划。 |
| [`az webapp config backup show`](https://docs.azure.cn/zh-cn/cli/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_show) | 显示 Web 应用的备份计划。 |
| [`az webapp config backup list`](https://docs.azure.cn/zh-cn/cli/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_list) | 获取 Web 应用的备份列表。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
