---
title: 如何更改、删除或管理管理组 - Azure 治理
description: 了解如何查看、维护、更新和删除管理组层次结构。
author: rthorn17
ms.service: azure-resource-manager
origin.date: 09/18/2018
ms.date: 04/22/2019
ms.author: v-biyu
ms.topic: conceptual
ms.openlocfilehash: 25f268035465d47c03107f9af4fa77736bb259e5
ms.sourcegitcommit: 5a7034098baffcc7979769b13790c1b487f073b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471972"
---
<!--Verify successfully-->
# <a name="manage-your-resources-with-management-groups"></a>使用管理组管理资源

如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 Azure 管理组提供订阅上的作用域级别。 可将订阅组织到名为“管理组”的容器中，并将管理条件应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的条件。

不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。  有关管理组的详细信息，请参阅[使用 Azure 管理组整理资源](index.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>更改管理组的名称

可以使用门户、PowerShell 或 Azure CLI 更改管理组的名称。

### <a name="change-the-name-in-the-portal"></a>在门户中更改名称

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 选择要重命名的管理组。

1. 选择页面顶部的“重命名组”选项。

   ![“管理组”页面上的“重命名组”选项](./media/detail_action_small.png)

1. 菜单打开后，请输入要显示的新名称。

   ![用于重命名管理组的“重命名组”窗格](./media/rename_context.png)

1. 选择“其他安全性验证” 。

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中更改名称

若要更新显示名称，请使用 **Update-AzManagementGroup**。 例如，若要将管理组的显示名称从“Contoso IT”更改为“Contoso Group”，请运行以下命令：

```PowerShell
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中更改名称

在 Azure CLI 中使用 update 命令。

```azurecli
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>删除管理组

若要删除某个管理组，必须满足以下要求：

1. 该管理组下面没有任何子管理组或订阅。

   - 若要将订阅移出管理组，请参阅[将订阅移到另一个管理组](#Move subscriptions in the hierarchy)。

   - 若要将管理组移到另一个管理组，请参阅[在层次结构中移动管理组](#Move management groups in the hierarchy)。

1. 你在管理组（“所有者”、“参与者”或“管理组参与者”）中拥有写入权限。 若要查看自己拥有哪些权限，请选择管理组，然后选择“IAM”。 有关 RBAC 角色的详细信息，请参阅[使用 RBAC 管理访问权限和权限](../../role-based-access-control/overview.md)。  

### <a name="delete-in-the-portal"></a>在门户中删除

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 选择要删除的管理组。

1. 选择“删除”

    > [!TIP]
    > 如果该图标已禁用，将鼠标指针悬停在该图标上可显示原因。

   ![“删除组”选项](./media/delete.png)

1. 此时会打开一个窗口，让你确认是否要删除该管理组。

   ![“确认删除组”窗口](./media/delete_confirm.png)

1. 请选择“是”。

### <a name="delete-in-powershell"></a>在 PowerShell 中删除

在 PowerShell 中使用 **Remove-AzManagementGroup** 命令删除管理组。

```PowerShell
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中删除

在 Azure CLI 中，可以使用 az account management-group delete 命令。

```azurecli
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>查看管理组

可以查看你对其拥有直接管理角色或继承 RBAC 角色的任何管理组。  

### <a name="view-in-the-portal"></a>在门户中查看

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 此时会加载管理组层次结构页。 可以在该页中浏览你有权访问的所有管理组和订阅。 选择组名会将你带到层次结构中的下一级别。 导航的工作方式与文件资源管理器一样。

1. 若要查看管理组的详细信息，请选择管理组标题旁边的“(详细信息)”链接。 如果此链接不可用，则表示你无权查看该管理组。

   ![主要](./media/main.png)

### <a name="view-in-powershell"></a>在 PowerShell 中查看

使用 Get-AzManagementGroup 命令检索所有组。  如需管理组 GET Powershell 命令的完整列表，请查看 [Az.Resources](https://docs.microsoft.com/powershell/module/az.resources/Get-AzManagementGroup) 模块。  

```azurepowershell
Get-AzManagementGroup
```

若要查看单个管理组的信息，请使用 -GroupName 参数

```azurepowershell
Get-AzManagementGroup -GroupName 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。  

```azurepowershell
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中查看

使用 list 命令可以检索所有组。  

```azurecli
az account management-group list
```

若要查看单个管理组的信息，请使用 show 命令

```azurecli
az account management-group show --name 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。

```azurecli
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>在层次结构中移动订阅

创建管理组的原因之一是将订阅捆绑在一起。 只能将管理组和订阅设置为另一个管理组的子级。 移到管理组的订阅从父管理组继承所有用户访问权限和策略。

若要移动订阅，则下述所有 RBAC 权限都必须为 true：

- 子订阅中的“所有者”角色。
- 目标父管理组上的“所有者”、“参与者”或“管理组参与者”角色。*
- 现有父管理组上的“所有者”、“参与者”或“管理组参与者”角色。*

*：除非目标父管理组或现有父管理组为根管理组。 由于根管理组是所有新管理组和订阅的默认登陆点，因此用户不需其上的权限即可移动项。

若要在 Azure 门户中查看自己拥有哪些权限，请选择管理组，然后选择“标识和访问管理”。 有关 RBAC 角色的详细信息，请参阅[使用 RBAC 管理访问权限和权限](../../role-based-access-control/overview.md)。

### <a name="move-subscriptions-in-the-portal"></a>在门户中移动订阅

#### <a name="add-an-existing-subscription-to-a-management-group"></a>将现有订阅添加到管理组

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加订阅”。

1. 在列表中选择具有正确 ID 的订阅。

   ![可以添加到管理组的订阅](./media/add_context_sub.png)

1. 选择“保存”。

#### <a name="remove-a-subscription-from-a-management-group"></a>从管理组删除订阅

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 选择要设为当前父级的管理组。  

1. 在列表中，选择要移动的订阅所在行末尾的椭圆。

   ![管理组上的“移动”选项](./media/move_small.png)

1. 选择“移动”。

1. 在打开的菜单中，选择“父管理组”。

   ![用于更改父组的“移动”窗格](./media/move_small_context.png)

1. 选择“其他安全性验证” 。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移动订阅

若要在 PowerShell 中移动订阅，请使用 New-AzManagementGroupSubscription 命令。  

```PowerShell
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

若要删除订阅与管理组之间的链接，请使用 Remove-AzManagementGroupSubscription 命令。

```PowerShell
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移动订阅

若要在 CLI 中移动订阅，请使用 add 命令。

```azurecli
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

若要从管理组中删除订阅，请使用 subscription remove 命令。  

```azurecli
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>在层次结构中移动管理组  

移动父管理组时，该组下的层次结构会随之移动。

### <a name="move-management-groups-in-the-portal"></a>在门户中移动管理组

1. 登录到 [Azure 门户](https://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加管理组”。

1. 在打开的菜单中，选择要使用新管理组或现有管理组。

   - 选择新管理组将创建一个新管理组。
   - 选择现有管理组将显示所有管理组的下拉列表，这些管理组可移动到此管理组。  

   ![将管理组移动到新的或现有组](./media/add_context_MG.png)

1. 选择“其他安全性验证” 。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移动管理组

在 PowerShell 中使用 Update-AzManagementGroup 命令将管理组移到不同的组下面。

```PowerShell
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移动管理组

在 Azure CLI 中使用 update 命令移动管理组。

```azurecli
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>使用活动日志审核管理组

[Azure 活动日志](../../azure-monitor/platform/activity-logs-overview.md)支持管理组。 可以查询与其他 Azure 资源相同的中心位置中的管理组发生的所有事件。  例如，可以看到对特定管理组所做的所有角色分配或策略分配更改。

![将活动日志与管理组配合使用](media/al-mg.png)

如果要在 Azure 门户外针对管理组进行查询，管理组的目标范围将如下所示：**"/providers/Microsoft.Management/managementGroups/{yourMgID}"**。

## <a name="referencing-management-groups-from-other-resource-providers"></a>引用其他资源提供程序的管理组

引用其他资源提供程序的操作中的管理组时，请使用以下路径作为范围。 此路径在使用 PowerShell、Azure CLI 和 REST API 时使用。  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

例如，在将新角色分配到 PowerShell 中的管理组时，使用此路径

```powershell
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

在管理组中检索策略定义时，使用同一范围路径。

```http
GET https://management.chinacloudapi.cn/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建用于整理 Azure 资源的管理组](create.md)
- [如何更改、删除或管理管理组](manage.md)
- [在 Azure PowerShell 资源模块中查看管理组](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.12.0#resources)
- [在 REST API 中查看管理组](https://docs.microsoft.com/en-us/rest/api/resources/managementgroups)
- [在 Azure CLI 中查看管理组](https://docs.azure.cn/zh-cn/cli/account/management-group?view=azure-cli-latest)
