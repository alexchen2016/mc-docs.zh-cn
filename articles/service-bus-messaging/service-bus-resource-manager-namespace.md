---
title: 使用 Azure 资源管理器模板创建服务总线消息传送命名空间 | Azure Docs
description: 使用 Azure 资源管理器模板创建服务总线消息命名空间
services: service-bus-messaging
documentationcenter: .net
author: lingliw
manager: digimobile
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: v-lingwu
ms.openlocfilehash: 90ec000da9fcda6d2cfcca154b9d700e0b86c48c
ms.sourcegitcommit: cca72cbb9e0536d9aaddba4b7ce2771679c08824
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58544809"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建服务总线命名空间
在本快速入门中，我们将创建一个 Azure 资源管理器模板，用于创建采用**标准** SKU 的类型为 **Messaging** 的服务总线命名空间。 本文还定义了为执行部署指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的详细信息，请参阅 [创作 Azure Resource Manager 模板][Authoring Azure Resource Manager templates]。 有关完整的模板，请参阅 GitHub 上的[服务总线命名空间模板][Service Bus namespace template]。

>[!NOTE]
> 以下 Azure Resource Manager 模板可供下载和部署。 
> 
> * [创建包含队列的服务总线命名空间](service-bus-resource-manager-namespace-queue.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要检查最新模板，请访问 [Azure 快速启动模板][Azure Quickstart Templates] 库并搜索服务总线。

## <a name="quick-deployment"></a>快速部署
若要运行该示例且不编写任何 JSON 和运行 PowerShell/CLI 命令，请选择以下按钮：

[![“部署到 Azure”](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

若要创建模板并手动部署，请通读本文中的以下部分。

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

若要使用 **Azure PowerShell** 部署资源管理器模板，请[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

若要使用 **Azure CLI** 部署资源管理器模板，请[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="create-the-resource-manager-template-json"></a>创建资源管理器模板 JSON 
创建包含以下内容的名为 **MyServiceBusNamespace.json** 的 JSON 文件： 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

此模板创建一个标准服务总线命名空间。

## <a name="create-the-parameters-json"></a>创建参数 JSON
在上一步骤中创建的模板包含名为 `Parameters` 的节。 请根据要部署的项目或根据目标环境，为这些值定义参数。 此模板定义以下参数：**serviceBusNamespaceName**、**serviceBusSku** 和 **location**。 若要详细了解服务总线的 SKU，请参阅[服务总线 SKU](https://www.azure.cn/pricing/details/service-bus/)。

创建包含以下内容的名为 **MyServiceBusNamespace-Parameters.json** 的 JSON 文件： 

> [!NOTE] 
> 指定服务总线命名空间的名称。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "China East"
    }
  }
}
```

## <a name="use-azure-powershell-to-deploy-the-template"></a>使用 Azure PowerShell 部署模板

### <a name="sign-in-to-azure"></a>登录 Azure
1. 启动 Azure PowerShell

2. 运行以下命令来登录到 Azure：

   ```azurepowershell
   Login-AzureRmAccount -Environment AzureChinaCloud
   ```
3. 如果发出以下命令来设置当前的订阅上下文：

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>部署资源
若要使用 Azure PowerShell 部署资源，请切换到 JSON 文件所保存到的文件夹，并运行以下命令：

> [!IMPORTANT]
> 在运行命令之前，请指定 Azure 资源组的名称作为 $resourceGroupName 的值。 

1. 为资源组名称声明一个变量，并为其指定值。 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. 创建 Azure 资源组。

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'China East'
    ```
3. 部署资源管理器模板。 指定部署本身、资源组、模板 JSON 文件和参数 JSON 文件的名称

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>使用 Azure CLI 部署模板

### <a name="sign-in-to-azure"></a>登录 Azure

1. 运行以下命令来登录到 Azure：

    ```azurecli
    az login
    ```
2. 设置当前订阅上下文。 将 `MyAzureSub` 替换为要使用的 Azure 订阅的名称：

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>部署资源
若要使用 Azure CLI 部署资源，请切换到包含 JSON 文件的文件夹，并运行以下命令：

> [!IMPORTANT]
> 在 az group create 命令中指定 Azure 资源组的名称。 上获取。

1. 创建 Azure 资源组。 
    ```azurecli
    az group create --name <YourResourceGroupName> --location chinaeast
    ```

2. 部署资源管理器模板。 指定资源组、部署、模板 JSON 文件、参数 JSON 文件的名称。

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>后续步骤
在本文中，我们已创建一个服务总线命名空间。 请参阅其他快速入门，了解如何创建和使用队列、主题/订阅： 

- [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
- [服务总线主题入门](service-bus-dotnet-how-to-use-topics-subscriptions.md)

  [Authoring Azure Resource Manager Templates]: ../azure-resource-manager/resource-group-authoring-templates.md
  [Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://github.com/Azure/azure-quickstart-templates/?term=service+bus
  [Service Bus pricing and billing]: ./service-bus-pricing-billing.md
  [Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md