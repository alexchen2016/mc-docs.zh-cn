---
title: Azure CLI 脚本示例 - 从 Azure 存储库创建应用并持续部署 | Azure Docs
description: Azure CLI 脚本示例 - 从 Azure 存储库使用持续部署创建应用
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
origin.date: 12/11/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: 9f550054bedd3f9bff8400a0ce652f7d5bfcbbf7
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083878"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>使用 Azure CLI 通过持续部署创建应用服务应用

此示例脚本使用其相关资源，在应用服务中创建应用，然后从 Azure DevOps 存储库设置持续部署。 在此示例中，需要以下项：

* 包含应用程序代码且你对其拥有管理权限的 Azure DevOps 存储库。
* Azure DevOps 组织的[个人访问令牌 (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]



如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-lastest)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

gitrepo=<Replace with your Visual Studio Team Services repo URL>
token=<Replace with a Visual Studio Team Services personal access token>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinanorth --name myResourceGroup

# Create an App Service plan in `FREE` tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# Configure continuous deployment from Visual Studio Team Services. 
# --git-token parameter is required only once per Azure account (Azure remembers token).
az webapp deployment source config --name $webappname --resource-group myResourceGroup \
--repo-url $gitrepo --branch master --git-token $token

# Copy the result of the following command into a browser to see the web app.
echo http://$webappname.chinacloudsites.cn
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/zh-cn/cli/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/zh-cn/cli/webapp?view=azure-cli-latest#az_webapp_create) | 创建 Azure Web 应用。 |
| [`az webapp deployment source config`](https://docs.azure.cn/zh-cn/cli/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config) | 将 Azure Web 应用与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
