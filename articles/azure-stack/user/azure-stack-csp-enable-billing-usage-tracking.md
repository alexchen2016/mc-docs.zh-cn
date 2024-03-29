---
title: 允许云服务提供商管理 Azure Stack 订阅 | Microsoft Docs
description: 允许云服务提供商访问 Azure Stack 中的订阅。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/19/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 686085d7a90b30b7f2697643394d3b7c73cc2f27
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855036"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果你通过云服务提供商 (CSP) 使用 Azure Stack，则可以选择管理自己的订阅以访问 Azure 和 Azure Stack 中的资源。 还可以让提供商管理你的订阅。 本文介绍以下操作：

* 让服务提供商访问你的订阅。
* 确保服务提供商可以管理你的服务。

> [!NOTE]
> 如果 CSP 没有管理你的帐户，并且你跳过以下步骤，则 CSP 将无法为你管理 Azure Stack 订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有用户角色的来宾用户添加到你的租户目录。 有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](/active-directory/add-users-azure-active-directory)
2. CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到你的租户目录。 有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](/active-directory/add-users-azure-active-directory)
2. 将“所有者”角色添加到 CSP 来宾用户。 有关将 CSP 用户添加到订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](/role-based-access-control/role-assignments-portal)。 CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../operator/azure-stack-billing-and-chargeback.md)。

<!-- Update_Description: wording update -->