---
title: 使用 Azure Active Directory B2C 设置通过微博帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过微博帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 09/11/2018
ms.date: 04/03/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 529ab33e4513f56e9a71e7ec1eec707de72f2c1a
ms.sourcegitcommit: 623e8f0d52c42d236ad2a0136d5aebd6528dbee3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67235965"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过微博帐户注册与登录

> [!NOTE]
> 此功能为预览版。
> 

## <a name="create-a-weibo-application"></a>创建 Weibo 应用程序

要将微博帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有微博帐户，可以在 [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) 获取。

1. 使用微博帐户凭据登录[微博开发人员门户](https://open.weibo.com/)。
2. 登录后，选择右上角的显示名称。
3. 在下拉列表中，选择“编辑开发者信息”  (edit developer information)。
4. 输入所需的信息，并选择“提交”  (submit)。
5. 完成电子邮件验证过程。
6. 转到[“身份验证”页](https://open.weibo.com/developers/identity/edit)。
7. 输入所需的信息，并选择“提交”  (submit)。

### <a name="register-a-weibo-application"></a>注册 Weibo 应用程序

1. 转到[“新 Weibo 应用注册”页](https://open.weibo.com/apps/new)。
2. 输入所需的应用程序信息。
3. 选择“创建”  (create)。
4. 复制**应用密钥**和**应用机密**的值。 将标识提供者添加到租户时需要这两项。
5. 上传所需的照片，并输入所需的信息。
6. 选择“保存以上信息”  (save)。
7. 选择“高级信息”  (advanced information)。
8. 选择 OAuth2.0“授权设置”  (redirect URL) 字段旁边的“编辑”  (edit)。
9. 为 OAuth2.0“授权设置”  (redirect URL) 输入 `https://your-tenant-name.b2clogin.cn/your-tenant-name.partner.onmschina.cn/oauth2/authresp`。 例如，如果租户名称是 contoso，请将 URL 设置为 `https://contoso.b2clogin.cn/contoso.partner.onmschina.cn/oauth2/authresp`。
10. 选择“提交”  (submit)。  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>将微博帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.cn/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”  ，然后选择“添加”  。
5. 提供“名称”  。 例如，输入“微博”  。
6. 选择“标识提供者类型”  ，选择“微博 (预览版)”  ，并单击“确定”  。
7. 选择“设置此标识提供者”  ，然后输入你之前记录为“客户端 ID”  的应用密钥，并输入你记录为之前创建的微博应用程序的“客户端机密”  的应用机密。
8. 单击“确定”  ，并单击“创建”  以保存 Weibo 配置。

