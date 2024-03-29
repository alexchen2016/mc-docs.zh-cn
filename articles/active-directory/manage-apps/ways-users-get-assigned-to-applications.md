---
title: 如何将用户分配给应用程序 | Microsoft Docs
description: 了解如何将用户分配给租户中的应用程序
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/11/2017
ms.date: 02/18/2019
ms.author: v-junlch
ms.openlocfilehash: 28b278a0c89150441c50ccbd31520688cbc00bca
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626069"
---
# <a name="how-to-assign-users-to-applications"></a>如何将用户分配给应用程序

本文介绍如何将用户分配给租户中的应用程序。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何将用户分配给 Azure AD 中的应用程序？

用户要访问应用程序，必须先以某种方式将其分配给该应用程序。 可使用管理员、业务委托，或有时使用用户本身的身份执行分配。 下文介绍了可以将用户分配给应用程序的方式：

1. 管理员直接[将用户分配给](/active-directory/active-directory-coreapps-assign-user-azure-portal)应用程序

2. 管理员[将用户是其成员的组分配](/active-directory/active-directory-coreapps-assign-user-azure-portal)给应用程序，包括：

   - 已从本地同步的组

   - 在云中创建的静态安全组

   - 在云中创建的 Office 365 组

   - [所有用户](/active-directory/fundamentals/active-directory-groups-create-azure-portal)组

3. 对于第一方应用程序，如 [ Microsoft Office 365](https://products.office.com/)，管理员直接为用户分配许可证

4. 对于第一方应用程序，如 [ Microsoft Office 365](https://products.office.com/)，管理员直接为用户是其成员的组分配许可证

5. [管理员同意](/active-directory/develop/active-directory-devhowto-multi-tenant-overview)所有用户均可使用某个应用程序，然后用户登录该应用程序

6. 通过登录应用程序，用户自己[同意使用应用程序](/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

<!-- Update_Description: link update -->


