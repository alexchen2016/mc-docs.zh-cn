---
title: 使用资源管理器模板创建策略分配
description: 本文逐步讲解如何使用资源管理器模板创建策略分配，以识别不合规的资源。
author: DCtheGeek
ms.author: v-biyu
origin.date: 03/13/2019
ms.date: 04/22/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 41e87918f959af8702655bbec454ed09cd8a21a8
ms.sourcegitcommit: 5a7034098baffcc7979769b13790c1b487f073b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471970"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>使用资源管理器模板创建策略分配以识别不合规的资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。
本快速入门逐步讲解如何创建策略分配，以识别未使用托管磁盘的虚拟机。

此过程结束时，你可以成功识别哪些虚拟机未使用托管磁盘。 这些虚拟机不符合策略分配要求。

如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="create-a-policy-assignment"></a>创建策略分配

在本快速入门中，我们将创建一个策略分配，并分配一个名为“审核不使用托管磁盘的 VM”的内置策略定义。 有关可用内置策略的部分列表，请参阅[策略示例](./samples/index.md)。

可通过多种方法创建策略分配。 本快速入门使用[快速入门模板](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)。
下面是该模板的副本：

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyAssignmentName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the policy assignment."
      }
    },
    "rgName":{
      "type": "string",
      "defaultValue": "[resourceGroup().name]",
      "metadata": {
        "description": "Specifies the name of the resource group where you want to assign the policy."
      }
    },
    "policyDefinitionID": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2018-05-01",
      "properties": {
        "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]"
      }
    }
  ]
}
```

> [!NOTE]
> Azure Policy 服务是免费的。  有关详细信息，请参阅 [Azure Policy 概述](./overview.md)。

1. 选择下图登录到 Azure 门户并打开模板：

   <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json"><img src="./media/assign-policy-template/deploy-to-azure.png" alt="deploy to azure"/></a>

1. 选择或输入以下值：

   | Name | 值 |
   |------|-------|
   | 订阅 | 选择 Azure 订阅。 |
   | 资源组 | 选择“新建”，指定名称，然后选择“确定”。 在屏幕截图中，资源组名称为 *mypolicyquickstart<Date in MMDD>rg*。 |
   | 位置 | 选择区域。 例如，**中国北部**。 |
   | 策略分配名称 | 指定策略分配名称。 如果需要，可以使用策略定义显示名称。 例如，“审核不使用托管磁盘的 VM”。 |
   | 资源组名称 | 指定要将策略分配到的资源组名称。 本快速入门使用默认值 **[resourceGroup().name]**。 **[resourceGroup()](/azure/azure-resource-manager/resource-group-template-functions-resource#resourcegroup)** 是检索资源组的模板函数。 |
   | 策略定义 ID | 指定 **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**。 |
   | 我同意上述条款和条件 | （选择） |

1. 选择“购买”。

其他某些资源：

- 若要了解如何开发资源管理器模板，请参阅 [Azure 资源管理器文档](/azure/azure-resource-manager/)。
- 若要了解订阅级部署，请参阅[在订阅级别创建资源组和资源](https://docs.azure.cn/zh-cn/azure-resource-manager/deploy-to-subscription)。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

选择页面左侧的“符合性”。 然后找到所创建的“审核未使用托管磁盘的 VM”策略分配。

![策略符合性概述页](./media/assign-policy-template/policy-compliance.png)

如果存在与此新分配不相符的任何现有资源，这些资源会在“不符合的资源”下显示。

有关详细信息，请参阅[合规工作原理](./how-to/get-compliance-data.md#how-compliance-works)。

## <a name="clean-up-resources"></a>清理资源

删除创建的分配，请执行以下步骤：

1. 选择“Azure Policy”页面左侧中的“符合性”（或“分配”）并找到你创建的“审核未使用托管磁盘的 VM”策略分配。

1. 右键单击“审核不使用托管磁盘的 VM”策略分配并选择“删除分配”。

   ![从符合性概述页中删除分配](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们在某个范围分配了一个内置策略定义并评估了其合规性报告。 策略定义可验证范围内的所有资源都符合策略，并可标识不符合策略的资源。

要了解有关分配策略以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)