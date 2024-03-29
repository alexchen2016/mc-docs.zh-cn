---
title: Azure CLI 脚本示例 - 将应用连接到存储帐户 | Azure Docs
description: Azure CLI 脚本示例 - 将应用连接到存储帐户
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 12/11/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: de12709876078aa893d20eb175ed732980dbad01
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083915"
---
# <a name="connect-an-app-service-app-to-a-storage-account-using-cli"></a>使用 CLI 将应用服务应用连接到存储帐户

此示例脚本创建一个 Azure 存储帐户和一个应用服务应用。 然后，它使用应用设置将存储帐户链接到应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-lastest)。


## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Variables
appName="webappwithstorage$random"
storageName="webappstorage$random"
location="ChinaNorth"

# Create a Resource Group 
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --location $location

# Create a Web App
az webapp create --name $appName --plan myAppServicePlan --resource-group myResourceGroup 

# Create a Storage Account
az storage account create --name $storageName --resource-group myResourceGroup --location $location --sku Standard_LRS

# Retreive the Storage Account connection string 
connstr=$(az storage account show-connection-string --name $storageName --resource-group myResourceGroup --query connectionString --output tsv)

# Assign the connection string to an App Setting in the Web App
az webapp config appsettings set --name $appName --resource-group myResourceGroup \
--settings "STORAGE_CONNSTR=$connstr"
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、存储帐户和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/zh-cn/cli/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/zh-cn/cli/webapp?view=azure-cli-latest#az_webapp_create) | 创建 Azure Web 应用。 |
| [`az storage account create`](https://docs.azure.cn/zh-cn/cli/storage/account?view=azure-cli-latest#az_storage_account_create) | 创建存储帐户。 |
| [`az storage account show-connection-string`](https://docs.azure.cn/zh-cn/cli/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string) | 为存储帐户获取连接字符串。 |
| [`az webapp config appsettings set`](https://docs.azure.cn/zh-cn/cli/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
