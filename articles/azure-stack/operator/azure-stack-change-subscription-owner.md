---
title: 更新 Azure Stack 用户订阅的所有者 | Microsoft Docs
description: 更改 Azure Stack 用户订阅的计费所有者。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
origin.date: 02/21/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 26125038fbd4e908aa2220bd4cddb122ee813f5f
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381881"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>更改 Azure Stack 用户订阅的所有者

Azure Stack 操作员可以使用 PowerShell 更改用户订阅的计费所有者。 例如，更改所有者的一个原因是替换已离职的用户。

会将两种类型的所有者分配到订阅： 

- **计费所有者**：默认情况下，计费所有者是从套餐获取订阅，然后拥有该订阅的计费关系的用户帐户。 此帐户也是订阅的管理员。 在一个订阅中，只能指定一个此类用户帐户。 计费所有者通常是组织或团队主管。

  可以使用 PowerShell cmdlet [Set-AzsUserSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/set-azsusersubscription) 更改计费所有者。  

- **通过 RBAC 角色添加的所有者** - 可以使用[基于角色的访问控制](azure-stack-manage-permissions.md) (RBAC) 系统为其他用户授予“所有者”  角色。 可将任意数量的其他用户帐户添加为所有者，以补充计费所有者。 其他所有者也是订阅的管理员，拥有订阅的所有特权，但无权删除计费所有者。

  可以使用 PowerShell 来管理其他所有者。 有关详细信息，请参阅[此文章](/role-based-access-control/role-assignments-powershell)。

## <a name="change-the-billing-owner"></a>更改计费所有者

运行以下脚本更改用户订阅的计费所有者。 用于运行该脚本的计算机必须连接到 Azure Stack 并运行 Azure Stack PowerShell 模块 1.3.0 或更高版本。 有关详细信息，请参阅[安装 Azure Stack PowerShell](azure-stack-powershell-install.md)。

运行脚本之前，请替换脚本中的以下值：

- **$ArmEndpoint**：环境的资源管理器终结点。
- **$TenantId**：租户 ID。
- **$SubscriptionId**：订阅 ID。
- **$OwnerUpn**：要添加为新账单所有者的帐户，例如 **user\@example.com**。

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>后续步骤

- [管理基于角色的访问控制](azure-stack-manage-permissions.md)
