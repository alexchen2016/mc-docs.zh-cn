---
title: 示例 - 审核密钥保管库是否没有虚拟网络终结点
description: 此示例策略定义审核 Key Vault 保管库以检测没有虚拟网络服务终结点的实例。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 01/26/2019
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 780428afc5c53492165d84f4341aedac48616e15
ms.sourcegitcommit: 1e5ca29cde225ce7bc8ff55275d82382bf957413
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56903191"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>示例 - 没有虚拟网络终结点的 Key Vault 保管库

此策略通过审核来查找没有虚拟网络终结点的 Key Vault 保管库。 用于强制执行安全要求。 有关详细信息，请参阅 [Key Vault 中的虚拟网络服务终结点](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)。

可以使用以下方法部署此示例策略：

- [Azure 门户](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>示例策略

### <a name="policy-definition"></a>策略定义

结构完整的 JSON 策略定义，可以通过 REST API、“部署到 Azure”按钮以及手动在门户中使用。

```json
{
   "properties": {
      "displayName": "Audit if Key Vault has no virtual network rules",
      "description": "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.azure.cn/zh-cn/key-vault/key-vault-overview-vnet-service-endpoints",
      "mode": "Indexed",
      "parameters": {},
      "policyRule": {
         "if": {
            "allOf": [
               {
                  "field": "type",
                  "equals": "Microsoft.KeyVault/vaults"
               },
               {
                  "anyOf": [
                     {
                        "field": "Microsoft.KeyVault/vaults/networkAcls.virtualNetworkRules[*].id",
                        "exists": "false"
                     },
                     {
                        "field": "Microsoft.KeyVault/vaults/networkAcls.virtualNetworkRules[*].id",
                        "notLike": "*"
                     },
                     {
                        "field": "Microsoft.KeyVault/vaults/networkAcls.defaultAction",
                        "equals": "Allow"
                     }
                  ]
               }
            ]
         },
         "then": {
            "effect": "audit"
         }
      }
   }
}
```

> [!NOTE]
> 如果手动在门户中创建策略，请使用上面的 **properties.parameters** 和 **properties.policyRule** 部分。 使用大括号 `{}` 将这两部分括在一起，使其成为有效的 JSON。

### <a name="policy-rules"></a>策略规则

定义了策略规则的 JSON，由 Azure CLI 和 Azure PowerShell 使用。

```json
{
   "if": {
      "allOf": [
         {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
         },
         {
            "anyOf": [
               {
                  "field": "Microsoft.KeyVault/vaults/networkAcls.virtualNetworkRules[*].id",
                  "exists": "false"
               },
               {
                  "field": "Microsoft.KeyVault/vaults/networkAcls.virtualNetworkRules[*].id",
                  "notLike": "*"
               },
               {
                  "field": "Microsoft.KeyVault/vaults/networkAcls.defaultAction",
                  "equals": "Allow"
               }
            ]
         }
      ]
   },
   "then": {
      "effect": "Audit"
   }
}
```

### <a name="policy-parameters"></a>策略参数

此示例策略定义尚未定义参数。

## <a name="azure-portal"></a>Azure 门户

[![“部署到 Azure”](http://azuredeploy.net/deploybutton.png)](https://portal.azure.cn)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

```azurepowershell
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.azure.cn/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>使用 Azure PowerShell 删除

运行以下命令来删除以前的分配和定义：

```azurepowershell
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell 说明

部署和删除脚本使用以下命令。 下表中的每条命令均链接到特定于命令的文档：

| 命令 | 注释 |
|---|---|
| [New-AzPolicyDefinition](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/New-Azpolicydefinition) | 创建新的 Azure Policy 定义。 |
| [Get-AzResourceGroup](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/Get-Azresourcegroup) | 获取单个资源组。 |
| [New-AzPolicyAssignment](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/New-Azpolicyassignment) | 创建新的 Azure Policy 分配。 在此示例中，我们向其提供了一个定义，但它也可以接受计划。 |
| [Remove-AzPolicyAssignment](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/Remove-Azpolicyassignment) | 删除现有的 Azure Policy 分配。 |
| [Remove-AzPolicyDefinition](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/Remove-Azpolicydefinition) | 删除现有的 Azure Policy 定义。 |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

```azurecli
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.azure.cn/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>使用 Azure CLI 进行删除

运行以下命令来删除以前的分配和定义：

```azurecli
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI 说明

| 命令 | 注释 |
|---|---|
| [az policy definition create](/cli/policy/definition?view=azure-cli-latest#az-policy-definition-create) | 创建新的 Azure Policy 定义。 |
| [az group show](/cli/group?view=azure-cli-latest#az-group-show) | 获取单个资源组。 |
| [az policy assignment create](/cli/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | 创建新的 Azure Policy 分配。 在此示例中，我们向其提供了一个定义，但它也可以接受计划。 |
| [az policy assignment delete](/cli/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | 删除现有的 Azure Policy 分配。 |
| [az policy definition delete](/cli/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | 删除现有的 Azure Policy 定义。 |

## <a name="rest-api"></a>REST API

有多个工具可以用来与资源管理器 REST API 进行交互，例如 [ARMClient](https://github.com/projectkudu/ARMClient) 或 PowerShell。

### <a name="deploy-with-rest-api"></a>使用 REST API 进行部署

- 创建策略定义（订阅范围）。 将[策略定义](#policy-definition) JSON 用于请求正文。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- 创建策略分配（资源组范围）

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  将以下 JSON 示例用于请求正文：

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>使用 REST API 进行删除

- 删除策略分配

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- 删除策略定义

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>REST API 说明

| 服务 | 组 | 操作 | 注释 |
|---|---|---|---|
| 资源管理 | 策略定义 | [创建](https://docs.microsoft.com/zh-cn/rest/api/resources/policydefinitions/createorupdate) | 在订阅中创建新的 Azure Policy 定义。 替换项：[在管理组中创建](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| 资源管理 | 策略分配 | [创建](https://docs.microsoft.com/zh-cn/rest/api/resources/policyassignments/create) | 创建新的 Azure Policy 分配。 在此示例中，我们向其提供了一个定义，但它也可以接受计划。 |
| 资源管理 | 策略分配 | [删除](https://docs.microsoft.com/zh-cn/rest/api/resources/policyassignments/delete) | 删除现有的 Azure Policy 分配。 |
| 资源管理 | 策略定义 | [删除](https://docs.microsoft.com/zh-cn/rest/api/resources/policydefinitions/delete) | 删除现有的 Azure Policy 定义。 替换项：[在管理组中删除](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>后续步骤

- 查看其他 [Azure Policy 示例](index.md)
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)