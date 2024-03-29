---
title: 创建启用了捕获的事件中心 - Azure 事件中心 | Azure Docs
description: 使用 Azure 资源管理器模板创建包含一个事件中心的 Azure 事件中心命名空间并启用捕获
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
origin.date: 08/16/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 716f8676eea7964b1297c855de622b84819b19ee
ms.sourcegitcommit: 1e5ca29cde225ce7bc8ff55275d82382bf957413
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56903011"
---
<!--Verify successfully-->
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>使用模板通过事件中心创建一个命名空间并启用捕获

本文介绍如何使用 Azure 资源管理器模板创建包含一个事件中心实例的[事件中心](event-hubs-what-is-event-hubs.md)命名空间，并在事件中心启用[捕获功能](event-hubs-capture-overview.md)。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

本文还介绍如何根据所选目标，指定将事件捕获到 Azure 存储 Blob 或 Azure Data Lake Store 中。

有关创建模板的详细信息，请参阅 [创作 Azure Resource Manager 模板][Authoring Azure Resource Manager templates]。

<!-- Not Available on [Azure Resources naming conventions][Azure Resources naming conventions]-->

如需完整的模板，请单击以下 GitHub 链接：

- [事件中心和“允许捕获到存储”模板][事件中心和“允许捕获到存储”模板] <!-- Not Available on [Event hub and enable Capture to Azure Data Lake Store template][Event Hub and enable Capture to Azure Data Lake Store template]-->

> [!NOTE]
> 若要检查最新模板，请访问 [Azure 快速启动模板][Azure Quickstart Templates] 库并搜索事件中心。
> 
> 

## <a name="what-will-you-deploy"></a>将部署什么内容？

借助此模板，可以部署包含事件中心的事件中心命名空间并启用[事件中心捕获](event-hubs-capture-overview.md)。 通过事件中心捕获，可以在所选的指定时间或大小间隔内，将事件中心的流数据自动传送到 Azure Blob 存储。 单击以下按钮，允许事件中心捕获将事件捕获到 Azure 存储中：

[![“部署到 Azure”](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<!--Not Available on Azure Data Lake Store-->

<!--Not Available on [![Deploy to Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)-->

## <a name="parameters"></a>parameters

使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

要创建的事件中心命名空间的名称。

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

在事件中心命名空间中创建的事件中心的名称。

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

要在事件中心内保留消息的天数。 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

事件中心内创建的分区数。

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

在事件中心启用捕获。

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

指定用于序列化事件数据的编码格式。

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

事件中心捕获开始捕获数据的时间间隔。

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
捕获开始捕获数据的大小间隔。

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

事件中心捕获用来编写 Avro 文件的名称格式。 注意，捕获名称格式必须包含 `{Namespace}`、`{EventHub}`、`{PartitionId}`、`{Year}`、`{Month}`、`{Day}`、`{Hour}`、`{Minute}`、`{Second}` 字段。 这些字段可以按任意顺序排列，可以带或不带分隔符。

```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }

```

### <a name="apiversion"></a>apiVersion

模板的 API 版本。

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

如果选择 Azure 存储作为目标，请使用以下参数。

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

捕获需要 Azure 存储帐户资源 ID，以启用到所需存储帐户的捕获。

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

用于捕获事件数据的 Blob 容器。

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>要针对 Azure 存储（作为已捕获事件的目标）进行部署的资源

创建包含一个事件中心的 EventHub 类型的命名空间，并启用“捕获到 Azure Blob 存储”。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```



## <a name="commands-to-run-deployment"></a>运行部署的命令

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

部署模板，允许事件中心捕获将事件捕获到 Azure 存储中：
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<!-- Not Availalble on Azure Data Lake Store and corresponding code -->
## <a name="azure-cli"></a>Azure CLI

Azure Blob 存储（作为目标）：

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

<!-- Not Availalble on Azure Data Lake Store and correspoding code -->

## <a name="next-steps"></a>后续步骤

也可通过 [Azure 门户](https://portal.azure.cn)配置事件中心捕获。 有关详细信息，请参阅[通过 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)。

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://github.com/Azure/azure-quickstart-templates/?term=event+hubs
<!-- Not Available on [Azure Resources naming conventions]: https://www.azure.cn/documentation/articles/guidance-naming-conventions/--> [事件中心和“允许捕获到存储”模板]： https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture<!-- Not Available on [Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls-->>

<!-- Update_Description: new articles on event hubs resource manager namespace event hub enbale capture -->
<!--ms.date: 09/17/2018-->