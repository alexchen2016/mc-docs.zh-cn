---
title: 示例 - 没有与 Express Route 网络对等互连的网络
description: 此示例策略定义禁止将网络对等互连关联到指定资源组中的网络。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 09/18/2018
ms.date: 03/11/2019
ms.author: dacoulte
ms.openlocfilehash: 23f8a1b0eef841cb76ecfa4fd92c96348fe282bd
ms.sourcegitcommit: 1e5ca29cde225ce7bc8ff55275d82382bf957413
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56903123"
---
# <a name="sample---no-network-peering-to-express-route-network"></a>示例 - 没有与 Express Route 网络对等互连的网络

此策略禁止将网络对等互联关联到指定资源组中的网络。 用于防止与中心托管的网络基础结构连接。 指定要防止关联的资源组的名称。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板


## <a name="sample-template"></a>示例模板
```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "resourceGroupName": {
                "type": "String",
                "metadata": {
                    "description": "Name of the resource group with ER Network",
                    "displayName": "ER Network Resource Group Name",
                    "strongType": "ExistingResourceGroups"
                }
            }
        },
        "displayName": "No network peering to ER network",
        "description": "No network peering can be associated to networks in network in a resource group containing central managed network infrastructure.",
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings"
                    },
                    {
                        "field": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings/remoteVirtualNetwork.id",
                        "contains": "[concat('resourceGroups/',parameters('resourceGroupName'))]"
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

[![“部署到 Azure”](http://azuredeploy.net/deploybutton.png)](https://portal.azure.cn/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fno-network-peerings-to-er-network%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = New-AzPolicyDefinition -Name "no-network-peerings-to-er-network" -DisplayName "No network peering to ER network" -description "No network peering can be associated to networks in network in a resource group containing central managed network infrastructure." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <ER Network Resource Group Name> -PolicyDefinition $definition
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
az policy definition create --name 'no-network-peerings-to-er-network' --display-name 'No network peering to ER network' --description 'No network peering can be associated to networks in network in a resource group containing central managed network infrastructure.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "no-network-peerings-to-er-network"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```cli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例
