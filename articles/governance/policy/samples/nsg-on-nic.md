---
title: 示例 - 每个 NIC 上的 NSG X
description: 此示例策略定义要求每个虚拟网络接口都使用特定的网络安全组。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 10/30/2017
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 09fc0bfc1541cb0772f3a7cd08a03fabbefc4d83
ms.sourcegitcommit: 1e5ca29cde225ce7bc8ff55275d82382bf957413
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56903238"
---
# <a name="sample---network-security-group-x-on-every-nic"></a>示例 - 每个 NIC 上的网络安全组 X

此策略需要每个虚拟网络接口使用特定的网络安全组。 指定要使用的网络安全组的 ID。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板
```json
{
    "properties": {
        "displayName": "NSG X on every nic",
        "description": "This policy enforces a specific NSG on every virtual network interface",
        "parameters": {
            "nsgId": {
                "type": "string",
                "metadata": {
                    "description": "Resource Id of the Network Security Group",
                    "displayName": "Network Security Group Id"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Network/networkInterfaces"
                    },
                    {
                        "not": {
                            "field": "Microsoft.Network/networkInterfaces/networkSecurityGroup.id",
                            "equals": "[parameters('nsgId')]"
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

[![“部署到 Azure”](http://azuredeploy.net/deploybutton.png)](https://portal.azure.cn/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fenforce-nsg-on-nic%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = New-AzPolicyDefinition -Name "enforce-nsg-on-nic" -DisplayName "NSG X on every nic" -description "This policy enforces a specific NSG on every virtual network interface" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -nsgId <Network Security Group Id> -PolicyDefinition $definition
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
az policy definition create --name 'enforce-nsg-on-nic' --display-name 'NSG X on every nic' --description 'This policy enforces a specific NSG on every virtual network interface' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-nic/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-nsg-on-nic"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```cli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例