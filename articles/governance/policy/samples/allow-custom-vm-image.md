---
title: 示例 - 允许使用来自资源组的自定义 VM 映像
description: 此示例策略定义要求自定义映像来自已批准的资源组。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 10/30/2017
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 38b988795bb2f64f144032b07bb7f5d6deccd346
ms.sourcegitcommit: 1e5ca29cde225ce7bc8ff55275d82382bf957413
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56903164"
---
# <a name="sample---allow-custom-vm-image-from-a-resource-group"></a>示例 - 允许使用来自资源组的自定义 VM 映像

此示例策略需要自定义映像来自批准的资源组。 指定已批准的资源组的名称。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板
```json
{
    "type": "Microsoft.Authorization/policyDefinitions",
    "name": "custom-image-from-rg",
    "properties": {
        "displayName": "Allow custom VM image from a Resoruce Group",
        "description": "This policy allows only usage of images from a resource group",
        "parameters": {
            "resourceGroupName":{
                "type":"String",
                "metadata":{
                    "displayName": "Resource Group Name",
                    "strongType": "ExistingResourceGroups"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Compute/virtualMachines"
                    },
                    {
                        "not": {
                            "field": "Microsoft.Compute/imageId",
                            "contains": "[concat('resourceGroups/',parameters('resourceGroupName'))]"
                        }
                    }
                ]
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```
可将 [Azure 门户](#deploy-with-the-portal)与 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 配合使用来部署此模板。

## <a name="deploy-with-the-portal"></a>使用门户进行部署

[![“部署到 Azure”](http://azuredeploy.net/deploybutton.png)](https://portal.azure.cn/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fcustom-image-from-rg%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = New-AzPolicyDefinition -Name "custom-image-from-rg" -DisplayName "Allow custom VM image from a Resource Group" -description "This policy allows only usage of images from a resource group" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <Resource Group Name> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清理 PowerShell 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy definition create --name 'custom-image-from-rg' --display-name 'Allow custom VM image from a Resource Group' --description 'This policy allows only usage of images from a resource group' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "custom-image-from-rg"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```cli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例