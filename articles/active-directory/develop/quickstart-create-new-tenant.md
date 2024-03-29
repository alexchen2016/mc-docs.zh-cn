---
title: 创建 Azure Active Directory 租户 | Microsoft Docs
description: 了解如何创建用于注册和生成应用程序的 Azure AD 租户。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
origin.date: 02/05/2019
ms.date: 04/08/2019
ms.author: v-junlch
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 746068cc7efb4d35efea06adf5a8629cbfc361f2
ms.sourcegitcommit: 1e18b9e4fbdefdc5466db81abc054d184714f2b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59243632"
---
# <a name="quickstart-set-up-a-dev-environment"></a>快速入门：设置开发环境

租户是组织的表示形式。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure 或 Microsoft 365）会收到该实例。 

每个 Azure AD 租户都与其他 Azure AD 租户不同并单独存在，而且使用自己的工作和学校标识以及应用注册进行表示。 租户内部的应用注册只允许从租户或所有租户的帐户中进行身份验证。 

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>工作和学校帐户或个人 Microsoft 帐户

### <a name="use-an-existing-tenant"></a>使用现有租户

许多开发人员已通过绑定到 Azure AD 租户的服务或订阅（例如 Microsoft 365 或 Azure 订阅）获得了租户。

1. 要检查租户，请使用要用于管理应用程序的帐户登录 [Azure 门户](https://portal.azure.cn)。
1. 查看右上角。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。
   * 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录和租户 ID (GUID) 以及域。
   * 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。

> [!TIP]
> 如果需要查找租户 ID，可执行以下操作：
> * 将鼠标指针悬停在帐户名上以获取目录/租户 ID，或
> * 在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”

如果没有任何与帐户关联的现有租户，则帐户名下面会显示一个 GUID；另外，除非按照下一节的步骤操作，否则无法执行注册应用等操作。

### <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

如果还没有 Azure AD 租户或想要为开发创建新租户，请遵循[目录创建体验](https://portal.azure.cn/#create/Microsoft.AzureActiveDirectory)。 必须提供以下信息才能创建新租户：

- **组织名称**
- **初始域** - 这将是 *.partner.onmschina.cn 的一部分。 稍后你可以更详细地自定义域。
- **国家或地区**

> [!NOTE]
> 对租户进行命名时，请使用字母数字字符。 不允许使用特殊字符。 名称不得超过 256 个字符。

## <a name="next-steps"></a>后续步骤

- 尝试编写快速入门代码并开始对用户进行身份验证。 
- 有关更多深入的代码示例，请参阅文档的“教程”部分。


<!-- Update_Description: wording update -->
