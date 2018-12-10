---
title: Azure CLI 脚本示例 - 将 Web 应用连接到 Redis 缓存 | Azure
description: Azure CLI 脚本示例 - 将 Web 应用连接到 Redis 缓存
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 12/11/2017
ms.date: 04/02/2018/
ms.author: v-yiso
ms.custom: mvc
ms.openlocfilehash: 5c7f3168744556c837bfc3edb485809f022fe8bd
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52645994"
---
# <a name="connect-a-web-app-to-a-redis-cache"></a>将 Web 应用连接到 Redis 缓存

此示例脚本创建一个 Azure Redis 缓存和一个 Azure Web 应用。 然后，将使用应用设置将 Redis 缓存链接到 Web 应用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/install-azure-cli)。
## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Variables
appName="webappwithredis$RANDOM"
location="chinanorth"

# Create a Resource Group 
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --location $location

# Create a Web App
az webapp create --name $appName --plan myAppServicePlan --resource-group myResourceGroup 

# Create a Redis Cache
redis=($(az redis create --name $appName --resource-group myResourceGroup \
--location $location --vm-size C0 --sku Basic --query [hostName,sslPort] --output tsv))

# Get access key
key=$(az redis list-keys --name $appName --resource-group myResourceGroup \
--query primaryKey --output tsv)

# Assign the connection string to an App Setting in the Web App
az webapp config appsettings set --name $appName --resource-group myResourceGroup \
 --settings "REDIS_URL=${redis[0]}" "REDIS_PORT=${redis[1]}" "REDIS_KEY=$key"
``` 
 
[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、Redis 缓存和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/zh-cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | 创建应用服务计划。 |
| [az webapp create](https://docs.azure.cn/zh-cn/cli/webapp#az_webapp_create) | 创建 Azure Web 应用。 |
| [`az redis create`](/cli/redis?view=azure-cli-latest#az_redis_create) | 创建新的 Redis 缓存实例。 |
| [`az redis list-keys`](/cli/redis?view=azure-cli-latest#az_redis_list_keys) | 列出 Redis 缓存实例的访问密钥。 |
| [az webapp config appsettings set](https://docs.azure.cn/zh-cn/cli/webapp/config/appsettings#az_webapp_config_appsettings_set) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。

<!--Update_Description: replace "az appservice web" with "az webapp"-->