---
title: Azure CLI 脚本示例 - 将自定义域映射到 Function App | Microsoft Docs
description: Azure CLI 脚本示例 - 将自定义域映射到 Azure 中的 Function App。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
origin.date: 07/04/2018
ms.date: 04/26/2019
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: 89872aa2c2f9221c17948387a801184cde538076
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855391"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>将自定义域映射到 Function App

此示例脚本在应用服务计划中创建函数应用，然后将其映射到你提供的自定义域。 当函数应用承载在[应用服务计划](../functions-scale.md#app-service-plan)中时，可以使用 CNAME 或 A 记录来映射自定义域。 对于[消耗计划](../functions-scale.md#consumption-plan)中的函数应用，仅支持 CNAME 选项。 此示例创建一个应用服务计划，并需要一个 A 记录来映射域。 

若要运行此示例脚本，必须已在自定义域中配置了一个指向 Web 应用的默认域名的 A 记录。 有关详细信息，请参阅[适用于 Azure 应用服务的映射自定义域说明](/app-service/app-service-web-tutorial-custom-domain#step-2-create-the-dns-records)。 

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

如果选择本地安装和使用 CLI，必须使用 Azure CLI 版本 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 


## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# Function app and storage account names must be unique.
storageName=mystorageaccount$RANDOM
functionAppName=myfunc$RANDOM

# TODO:
# Before starting, go to your DNS configuration UI for your custom domain and follow the 
# instructions at https://aka.ms/appservicecustomdns to configure an A record 
# and point it your web app's default domain name. 
fqdn=<Replace with www.{yourcustomdomain}>

# Create a resource resourceGroupName
az group create `
  --name myResourceGroup `
  --location chinanorth

# Create an azure storage account
az storage account create `
  --name $storageName `
  --location chinanorth `
  --resource-group myResourceGroup `
  --sku Standard_LRS

# Create an App Service plan in Basic tier (minimum required by custom domains).
az appservice plan create `
  --name FunctionAppWithAppServicePlan `
  --resource-group myResourceGroup `
  --location chinanorth `
  --sku B1

# Create a Function App
az functionapp create `
  --name $functionAppName `
  --storage-account $storageName `
  --plan FunctionAppWithAppServicePlan `
  --resource-group myResourceGroup
  
# Map your prepared custom domain name to the function app.
az functionapp config hostname add `
  --hostname $functionAppName `
  --resource-group myResourceGroup `
  --name $fqdn

echo "You can now browse to http://$fqdn"
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/storage/account#az-storage-account-create) | 创建 Function App 所需的存储帐户。 |
| [az appservice plan create](/cli/appservice/plan#az-appservice-plan-create) | 创建映射自定义域所需的应用服务计划。 |
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 在应用服务计划中创建函数应用。 |
| [az functionapp config hostname add](/cli/functionapp/config/hostname#az-functionapp-config-hostname-add) | 将自定义域映射到 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Functions CLI 脚本示例。

