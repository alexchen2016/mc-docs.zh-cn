---
title: 配置令牌 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中配置令牌生存期和兼容性设置。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 04/16/2019
ms.date: 06/05/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 4fe356189c47859bb2d1441b76f73fd2c55d66c5
ms.sourcegitcommit: 26e99f63fe3c2ffbdcdcc17691199bbacabdd048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687651"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置令牌

本文介绍如何在 Azure Active Directory (Azure AD) B2C 中配置[令牌的生存期和兼容性](active-directory-b2c-reference-tokens.md)。

## <a name="prerequisites"></a>先决条件

[创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。

## <a name="configure-token-lifetime"></a>配置令牌生存期

可以在任何用户流上配置令牌生存期。

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录和订阅筛选器”，然后选择包含 Azure AD B2C 租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
4. 选择“用户流(策略)”  。
5. 打开之前创建的用户流。 
6. 选择“属性”  。
7. 在“令牌生存期”  下，调整以下属性以满足应用程序的需要：

    ![配置令牌生存期](./media/configure-tokens/token-lifetime.png)

8. 单击“保存”  。

## <a name="configure-token-compatibility"></a>配置令牌兼容性

1. 选择“用户流(策略)”  。
2. 打开之前创建的用户流。 
3. 选择“属性”  。
4. 在“令牌兼容性设置”  下，调整以下属性以满足应用程序的需要：

    ![配置令牌兼容性](./media/configure-tokens/token-compatibility.png)

5. 单击“保存”  。

## <a name="next-steps"></a>后续步骤

详细了解如何[使用访问令牌](active-directory-b2c-access-tokens.md)。




