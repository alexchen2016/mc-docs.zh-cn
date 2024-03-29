---
title: 使用 Azure 资源管理器创建多个资源实例 | Azure
description: 了解如何创建 Azure 资源管理器模板用于创建多个 Azure 资源实例。
services: azure-resource-manager
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
origin.date: 03/04/2019
ms.date: 04/15/2019
ms.topic: tutorial
ms.author: v-yeche
ms.openlocfilehash: 0e5ecfbf518daa636d6a137dcd7d3763eb008304
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529171"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>教程：使用资源管理器模板创建多个资源实例

了解如何在 Azure 资源管理器模板中进行迭代操作，以创建 Azure 资源的多个实例。 在本教程中，你将修改一个模板，以便创建三个存储帐户实例。

![“Azure 资源管理器创建多个实例”示意图](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 编辑模板
> * 部署模板

如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含[资源管理器工具扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)的 [Visual Studio Code](https://code.visualstudio.com/)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本快速入门中使用的模板称为[创建标准存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 在模板中定义了一个“Microsoft.Storage/storageAccounts”资源。
    
   <!-- Not Available on [template reference](https://docs.microsoft.com/zh-cn/azure/templates/Microsoft.Storage/storageAccounts)-->

5. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。

## <a name="edit-the-template"></a>编辑模板

现有模板创建一个存储帐户。 请通过自定义模板来创建三个存储帐户。  

在 Visual Studio Code 中进行以下四项更改：

![Azure 资源管理器创建多个实例](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 将 `copy` 元素添加到存储帐户资源定义。 在 copy 元素中，为此循环指定迭代次数和变量。 计数值必须是不超过 800 的正整数。
2. `copyIndex()` 函数返回循环中的当前迭代。 使用索引作为名称前缀。 `copyIndex()` 从零开始。 若要偏移索引值，可以在 copyIndex() 函数中传递一个值。 例如 *copyIndex(1)*。
3. 删除 **variables** 元素，因为不再需要使用它。
4. 删除 **outputs** 元素。 不再需要它。

完成的模板如下所示：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

有关创建多个实例的详细信息，请参阅[在 Azure 资源管理器模板中部署一个资源或属性的多个实例](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>部署模板

有关部署过程，请参阅 Visual Studio Code 快速入门中的[部署模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)部分。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要列出所有三个存储帐户，请省略 --name 参数：

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

将存储帐户名称与模板中的名称定义进行比较。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解如何创建多个存储帐户实例。  在下一篇教程中，我们将开发包含多个资源和多个资源类型的模板。 某些资源具有依赖的资源。

> [!div class="nextstepaction"]
> [创建依赖资源](./resource-manager-tutorial-create-templates-with-dependent-resources.md)

<!-- Update_Description: wording update, update meta properties, update link -->
