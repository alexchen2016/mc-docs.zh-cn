---
title: Azure CLI 脚本示例 - 将应用连接到 SQL 数据库 | Azure Docs
description: Azure CLI 脚本示例 - 将应用连接到 SQL 数据库
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 12/11/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: 145070cdca84ae0f1a8c59051f27aa427302bd01
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083836"
---
# <a name="connect-an-app-service-app-to-a-sql-database-using-cli"></a>使用 CLI 将应用服务应用连接到 SQL 数据库

此示例脚本创建一个 Azure SQL 数据库和一个应用服务应用。 然后，它将使用应用设置将 SQL 数据库链接到应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-lastest)。

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Variables
appName="webappwithSQL$RANDOM"
serverName="webappwithsql$RANDOM"
location="ChinaNorth"
startip="0.0.0.0"
endip="0.0.0.0"
username="<replace-with-username>"
sqlServerPassword="<replace-with-password>"

# Create a resource group 
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --location $location

# Create a Web App
az webapp create --name $appName --plan myAppServicePlan --resource-group myResourceGroup

# Create a SQL Database server
az sql server create --name $serverName --resource-group myResourceGroup --location $location --admin-user $username --admin-password $sqlServerPassword

# Configure firewall for Azure access
az sql server firewall-rule create --server $serverName --resource-group myResourceGroup \
--name AllowYourIp --start-ip-address $startip --end-ip-address $endip

# Create a database called 'MySampleDatabase' on server
az sql db create --server $serverName --resource-group myResourceGroup --name MySampleDatabase \
--service-objective S0

# Get connection string for the database
connstring=$(az sql db show-connection-string --name MySampleDatabase --server $serverName \
--client ado.net --output tsv)

# Add credentials to connection string
connstring=${connstring//<username>/$username}
connstring=${connstring//<password>/$sqlServerPassword}

# Assign the connection string to an app setting in the web app
az webapp config appsettings set --name $appName --resource-group myResourceGroup \
--settings "SQLSRV_CONNSTR=$connstring" 
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、SQL 数据库和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/zh-cn/cli/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/zh-cn/cli/webapp?view=azure-cli-latest#az_webapp_create) | 创建 Azure Web 应用。 |
| [`az sql server create`](https://docs.azure.cn/zh-cn/cli/sql/server?view=azure-cli-latest#az_sql_server_create) | 创建 SQL 数据库服务器。  |
| [`az sql db create`](https://docs.azure.cn/zh-cn/cli/sql/db?view=azure-cli-latest#az_sql_db_create) | 使用 SQL 数据库服务器创建新的数据库。 |
| [`az sql db show-connection-string`](https://docs.azure.cn/zh-cn/cli/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string) | 生成数据库的连接字符串。 |
| [`az webapp config appsettings set`](https://docs.azure.cn/zh-cn/cli/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。

<!--Update_Description: add a note about Azure CLI 2.0 version-->