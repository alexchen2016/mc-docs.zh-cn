---
title: 创建具有使用者组的事件中心 - Azure 事件中心 | Azure Docs
description: 使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
origin.date: 08/16/2018
ms.date: 05/06/2019
ms.author: v-biyu
ms.openlocfilehash: 709e0dbae3323066897a9cf5867a28214cac904b
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855199"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建事件中心
Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

在此快速入门中，使用 Azure 资源管理器模板创建事件中心。 使用 Azure 资源管理器模板创建包含一个事件中心和一个使用者组的[事件中心](event-hubs-what-is-event-hubs.md)类型命名空间。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的信息，请参阅[创作 Azure 资源管理器模板][Authoring Azure Resource Manager templates]。

> [!NOTE]
> 有关完整的模板，请参阅 GitHub 上的[事件中心和使用者组模板][Event Hub and consumer group template]。 除了一个事件中心命名空间和一个事件中心之外，此模板还创建了一个使用者组。 若要检查最新模板，请访问 [Azure 快速启动模板][Azure Quickstart Templates] 库并搜索事件中心。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

若要使用 **Azure PowerShell** 部署资源管理器模板，请[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 **Azure CLI** 部署资源管理器模板，请[安装 Azure CLI]( /cli/install-azure-cli)。

## <a name="create-the-resource-manager-template-json"></a>创建资源管理器模板 JSON
创建包含以下内容且名为 MyEventHub.json 的 JSON 文件，并将其保存到某个文件夹（例如：C:\EventHubsQuickstarts\ResourceManagerTemplate）。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "China North",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "China North",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>创建参数 JSON
创建一个名为 MyEventHub-Parameters.json 且包含 Azure 资源管理器模板参数的 JSON 文件。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>使用 Azure PowerShell 部署模板

### <a name="sign-in-to-azure"></a>登录 Azure
1. 启动 Azure PowerShell

2. 运行以下命令来登录到 Azure：

   ```azurepowershell
   Login-AzAccount -EnvironmentName AzureChinaCloud
   ```
3. 如果发出以下命令来设置当前的订阅上下文：

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>预配资源
若要使用 Azure PowerShell 部署/预配资源，请切换到 C:\EventHubsQuickStart\ARM\ 文件夹，然后运行以下命令：

> [!IMPORTANT]
> 在运行命令之前，指定 Azure 资源组的名称作为 $resourceGroupName 的值。 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'China North'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>使用 Azure CLI 部署模板

## <a name="sign-in-to-azure"></a>登录 Azure

如果在本地运行 CLI，请按以下步骤登录到 Azure 并设置当前订阅：

运行以下命令来登录到 Azure：

```azurecli
az cloud set -n AzureChinaCloud
az login
```

设置当前订阅上下文。 将 `MyAzureSub` 替换为要使用的 Azure 订阅的名称：

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>预配资源
若要使用 Azure CLI 部署资源，请切换到 C:\EventHubsQuickStart\ARM\ 文件夹，然后运行以下命令：

> [!IMPORTANT]
> 在 az group create 命令中指定 Azure 资源组的名称。 上获取。

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location chinanorth

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

祝贺！ 现已使用 Azure 资源管理器模板创建了一个事件中心命名空间，并在该命名空间中创建了一个事件中心。

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建事件中心命名空间，并使用示例应用程序从事件中心发送和接收事件。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅**发送和接收事件**教程： 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C（仅发送）](event-hubs-c-getstarted-send.md)
- [Apache Storm（仅接收）](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://github.com/Azure/azure-quickstart-templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
