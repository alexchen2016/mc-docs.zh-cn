---
title: Azure Stack 的标识体系结构 | Microsoft Docs
description: 了解可与 Azure Stack 配合使用的标识体系结构。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 05/09/2019
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: b925839fa070e56540caf10821f18317f90ca54c
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381925"
---
# <a name="identity-architecture-for-azure-stack"></a>Azure Stack 的标识体系结构

选择要与 Azure Stack 配合使用的标识提供者时，应了解 Azure Active Directory (Azure AD) 的选项与 Active Directory 联合身份验证服务 (AD FS) 的选项之间的重要区别。

## <a name="capabilities-and-limitations"></a>功能和限制 
选择的标识提供者可能会限制可用的选项，包括对多租户的支持。 

  

|功能或方案        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|连接到 Internet     |是       |可选|
|对多租户的支持     |是       |否      |
|在市场中提供商品 |是       |是的。 需要使用[脱机市场联合](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)工具。|
|对 Active Directory 身份验证库 (ADAL) 的支持 |是 |是|
|支持 Azure CLI、Visual Studio 和 PowerShell 等工具  |是 |是|
|通过 Azure 门户创建服务主体     |是 |否|
|使用证书创建服务主体      |是 |是|
|使用机密（密钥）创建服务主体    |是 |是|
|应用程序可以使用 Graph 服务           |是 |否|
|应用程序可以将标识提供程序用于登录 |是 |是的。 要求应用程序与本地 AD FS 实例联合。 |

## <a name="topologies"></a>拓扑
以下部分介绍可用的各种标识拓扑。

### <a name="azure-ad-single-tenant-topology"></a>Azure AD：单租户拓扑 
默认情况下，当安装 Azure Stack 并使用 Azure AD 时，Azure Stack 使用单租户拓扑。 

单租户拓扑非常适用于下列情况：
- 所有用户都属于同一租户。
- 服务提供程序托管着组织的 Azure Stack 实例。 

![结合 Azure AD 的 Azure Stack 单租户拓扑](media/azure-stack-identity-architecture/single-tenant.png)

此拓扑具有以下特征：
- Azure Stack 将所有应用程序和服务注册到同一 Azure AD 租户目录。 
- Azure Stack 仅通过该目录对用户和应用程序进行身份验证，包括令牌。 
- 管理员（云操作员）和租户用户的标识位于同一目录租户中。 
- 若要使其他目录中的用户能够访问此 Azure Stack 环境，必须[将用户作为来宾邀请](azure-stack-identity-overview.md#guest-users)到该租户目录。 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD：多租户拓扑
云操作员可以将 Azure Stack 配置为允许一个或多个组织中的租户访问应用程序。 用户通过用户门户访问应用程序。 在此配置中，管理员门户（由云操作员使用）仅限单个目录中的用户访问。 

多租户拓扑非常适用于下列情况：
- 服务提供商希望允许多个组织中的用户访问 Azure Stack。

![结合 Azure AD 的 Azure Stack 多租户拓扑](media/azure-stack-identity-architecture/multi-tenant.png)

此拓扑具有以下特征：
- 对资源的访问权限应当以组织为单位。 
- 一个组织中的用户不应当能够向其组织外部的用户授予对资源的访问权限。 
- 管理员（云操作员）标识可以位于与用户标识所在目录租户不同的目录租户中。 此分离在标识提供者级别提供了帐户隔离。 
 
### <a name="ad-fs"></a>AD FS  
当以下任一情况属实时，需要使用 AD FS 拓扑：
- Azure Stack 不会连接到 Internet。
- Azure Stack 可以连接到 Internet，但你选择为标识提供者使用 AD FS。
  
![使用 AD FS 的 Azure Stack 拓扑](media/azure-stack-identity-architecture/adfs.png)

此拓扑具有以下特征：
- 为了支持在生产环境中使用此拓扑，必须通过联合信任将内置的 Azure Stack AD FS 实例与由 Active Directory 提供支持的现有 AD FS 实例进行集成。 
- 可将 Azure Stack 中的 Graph 服务与现有的 Active Directory 实例集成。 还可以使用基于 OData 的图形 API 服务，该服务支持与 Azure AD 图形 API 一致的 API。 

  若要与 Active Directory 实例交互，图形 API 要求使用 Active Directory 实例中拥有只读权限的用户凭据。 
  - 内置的 AD FS 实例基于 Windows Server 2016。 
  - AD FS 和 Active Directory 实例必须基于 Windows Server 2012 或更高版本。 
  
  Active Directory 实例与内置 AD FS 实例之间的交互不限于 OpenID Connect，两者可以使用相互支持的任何协议。 
  - 在本地 Active Directory 实例中创建和管理用户帐户。
  - 在内置 Active Directory 实例中管理应用程序的服务主体和注册。



## <a name="next-steps"></a>后续步骤
- [标识概述](azure-stack-identity-overview.md)   
- [数据中心集成 - 标识](azure-stack-integrate-identity.md)
