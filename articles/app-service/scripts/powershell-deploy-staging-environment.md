---
title: Azure PowerShell 脚本示例 - 创建应用并将代码部署到过渡槽
description: Azure PowerShell 脚本示例 - 创建 Web 应用并将代码部署到过渡环境
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
origin.date: 03/20/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: mvc
ms.openlocfilehash: 4f87680505cac8c14b799be84b2048a7acddbfe2
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083922"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>创建 Web 应用并将代码部署到过渡环境

此示例脚本使用名为“过渡”的附加部署槽在应用服务中创建 Web 应用，并将示例应用部署到“过渡”槽。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount -EnvironmentName AzureChinaCloud` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell
# Replace the following URL with a public GitHub repo URL
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"
$webappname="mywebapp$(Get-Random)"
$location="China North"

# Create a resource group.
New-AzureRmResourceGroup -Name myResourceGroup -Location $location

# Create an App Service plan in Free tier.
New-AzureRmAppServicePlan -Name $webappname -Location $location `
-ResourceGroupName myResourceGroup -Tier Free

# Create a web app.
New-AzureRmWebApp -Name $webappname -Location $location `
-AppServicePlan $webappname -ResourceGroupName myResourceGroup

# Upgrade App Service plan to Standard tier (minimum required by deployment slots)
Set-AzureRmAppServicePlan -Name $webappname -ResourceGroupName myResourceGroup `
-Tier Standard

#Create a deployment slot with the name "staging".
New-AzureRmWebAppSlot -Name $webappname -ResourceGroupName myResourceGroup `
-Slot staging

# Configure GitHub deployment to the staging slot from your GitHub repo and deploy once.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName myResourceGroup `
-ResourceType Microsoft.Web/sites/slots/sourcecontrols `
-ResourceName $webappname/staging/web -ApiVersion 2015-08-01 -Force

# Swap the verified/warmed up staging slot into production.
Switch-AzureRmWebAppSlot -Name $webappname -ResourceGroupName myResourceGroup `
-SourceSlotName staging -DestinationSlotName production
```

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [Set-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/module/azurerm.websites/set-azurermappserviceplan) | 修改应用服务计划以更改其定价层。 |
| [New-AzureRmWebAppSlot](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermwebappslot) | 为 Web 应用创建部署槽。 |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/set-azurermresource) | 修改资源组中的资源。 |
| [Switch-AzureRmWebAppSlot](https://docs.microsoft.com/en-us/powershell/module/azurerm.websites/switch-azurermwebappslot?view=azurermps-5.7.0) | 将 Web 应用的部署槽交换到生产环境。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。