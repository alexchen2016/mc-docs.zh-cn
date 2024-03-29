---
title: 如何为 Azure Stack 创建注册角色
description: 如何创建自定义角色，以避免使用全局管理员身份进行注册。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/13/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: ef657a79b1980ed91e1739d2c11a4d23aae95e4a
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381783"
---
# <a name="create-a-registration-role-for-azure-stack"></a>为 Azure Stack 创建注册角色

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

如果你不想在 Azure 订阅中授予所有者权限，可以创建一个自定义角色，以将权限分配给某个用户帐户来注册 Azure Stack。

> [!WARNING]
> 这不是一种安全态势功能。 如果你想要实施约束来防止意外更改 Azure 订阅，可以使用此功能。 向某个用户委托此自定义角色的权限时，该用户有权编辑权限和提升权限。 请只将受信任的用户分配到自定义角色。

注册 Azure Stack 时，注册帐户需要以下 Azure Active Directory 权限和 Azure 订阅权限：

* **Azure Active Directory 租户中的应用程序注册权限：** 管理员拥有应用程序注册权限。 用户的权限是租户中所有用户的全局设置。 若要查看或更改设置，请参阅[创建可访问资源的 Azure AD 应用程序和服务主体](/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

    “用户可以注册应用程序”设置必须设置为“是”才能让用户帐户注册 Azure Stack。   如果“应用注册”设置指定为“否”，则你无法使用用户帐户，而必须使用全局管理员帐户来注册 Azure Stack。 

* **一组足够高的 Azure 订阅权限：** “所有者”组中的用户拥有足够高的权限。 对于其他帐户，可以通过分配自定义角色来分配权限集，如以下部分所述。

## <a name="create-a-custom-role-using-powershell"></a>使用 PowerShell 创建自定义角色

若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](/role-based-access-control/built-in-roles#owner)或[用户访问权限管理员](/role-based-access-control/built-in-roles#user-access-administrator)。 使用以下 JSON 模板来简化自定义角色的定义。 该模板创建允许对 Azure Stack 注册进行必要读取和写入访问的自定义角色。

1. 创建一个 JSON 文件。 例如 `C:\CustomRoles\registrationrole.json`
2. 将以下 JSON 添加到该文件。 将 `<SubscriptionID>` 替换为你的 Azure 订阅 ID。

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，连接到 Azure 以使用 Azure 资源管理器。 出现提示时，请使用[所有者](/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/role-based-access-control/built-in-roles#user-access-administrator)等拥有足够权限的帐户进行身份验证。

    ```azurepowershell
    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud
    ```

4. 若要将角色添加到订阅，请使用 **New-AzureRmRoleDefinition** 并指定 JSON 模板文件。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>将用户分配到注册角色

创建注册自定义角色后，分配用于注册 Azure Stack 的角色用户。

1. 使用在 Azure 订阅中拥有足够权限的帐户登录，以委托权限 - 例如[所有者](/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/role-based-access-control/built-in-roles#user-access-administrator)。
2. 在“订阅”中，选择“访问控制(IAM)”>“添加角色分配”。  
3. 在“角色”中，选择创建的自定义角色“Azure Stack 注册角色”。  
4. 选择要分配到该角色的用户。
5. 选择“保存”，将选定的用户分配到该角色。 

    ![选择要分配到角色的用户](media/azure-stack-registration-role/assign-role.png)

有关使用自定义角色的详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](/role-based-access-control/role-assignments-portal)。

## <a name="next-steps"></a>后续步骤

[将 Azure Stack 注册到 Azure](azure-stack-registration.md)
