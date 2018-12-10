---
title: 在 Azure 资源管理器模板中使用条件 | Azure
description: 了解如何根据条件部署 Azure 资源。
services: azure-resource-manager
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
origin.date: 10/02/2018
ms.date: 11/19/2018
ms.topic: tutorial
ms.author: v-yeche
ms.openlocfilehash: 5be337cb35773d61cd2dd890cf7ad0a4c73155be
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52650701"
---
<!--Verify sucessfully-->
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>教程：在 Azure 资源管理器模板中使用条件

了解如何根据条件部署 Azure 资源。 

本教程中使用的方案类似于[教程：使用依赖资源创建 Azure 资源管理器模板](./resource-manager-tutorial-create-templates-with-dependent-resources.md)中使用的方案。 本教程介绍如何创建存储帐户、虚拟机、虚拟网络以及其他一些依赖资源。 无需创建新的存储帐户，可让用户选择创建新的存储帐户，或者使用现有的存储帐户。 为实现此目的，需定义附加的参数。 如果参数值为“new”，则创建新存储帐户。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 修改模板
> * 部署模板
> * 清理资源

如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含[资源管理器工具扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)的 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件的副本保存到名为 **azuredeploy.json** 的本地计算机。

## <a name="modify-the-template"></a>修改模板

对现有模板进行两项更改：

* 添加用于提供存储帐户名称的参数。 此参数可让用户选择指定现有的存储帐户名称。 它也可以用作新存储帐户名称。
* 添加名为 **newOrExisting** 的新参数。 部署使用此参数来确定是要创建新存储帐户还是使用现有的存储帐户。

1. 在 Visual Studio Code 中打开 **azuredeploy.json**。
2. 在整个模板中，将 **variables('storageAccountName')** 替换为 **parameters('storageAccountName')**。  **variables('storageAccountName')** 有三种外观。
3. 删除以下变量定义：

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. 将以下两个参数添加到模板：

    ```json
    "newOrExisting": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    ```
    更新的参数定义如下所示：

    ![在资源管理器中使用条件](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. 将以下行添加到存储帐户定义的开头。

    ```json
    "condition": "[equals(parameters('newOrExisting'),'yes')]",
    ```

    该条件检查名为 **newOrExisting** 的参数的值。 如果参数值为 **new**，则部署将创建存储帐户。

    更新的存储帐户定义如下所示：

    ![在资源管理器中使用条件](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)

6. 保存更改。

## <a name="deploy-the-template"></a>部署模板

遵照[部署模板](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)中的说明部署模板。

使用 Azure PowerShell 部署模板时，需要指定一个附加参数：

```powershell
$resourceGroupName = "<Enter the resource group name>"
$storageAccountName = "Enter the storage account name>"
$location = "<Enter the Azure location>"
$vmAdmin = "<Enter the admin username>"
$vmPassword = "<Enter the password>"
$dnsLabelPrefix = "<Enter the prefix>"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting "new"
```

> [!NOTE]
> 如果 **newOrExisting** 为 **new**，但具有指定存储帐户名称的存储帐户已存在，则部署将会失败。

请尝试创建 **newOrExisting** 设置为“existing”的另一个部署，并指定现有存储帐户。 若要提前创建存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们开发了一个允许用户选择创建新存储帐户或使用现有存储帐户的模板。 本教程中创建的虚拟机需要管理员用户名和密码。 在部署期间无需传递密码，可以使用 Azure Key Vault 预先存储密码，并在部署期间检索该密码。 若要了解如何从 Azure Key Vault 检索机密并在模板部署中使用这些机密，请参阅：

> [!div class="nextstepaction"]
> [在模板部署中集成 Key Vault](./resource-manager-tutorial-use-key-vault.md)

<!-- Update_Description: new articles on resource manager turorial use conditions -->
<!--ms.date: 11/19/2018-->