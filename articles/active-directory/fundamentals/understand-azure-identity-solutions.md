---
title: 什么是 Azure 标识？ | Microsoft Docs
description: 了解 Azure 标识解决方案的术语、概念和建议，以便为组织做出最明智的标识监管决策。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
origin.date: 07/17/2017
ms.date: 11/12/2018
ms.author: v-junlch
ms.reviewer: jsnow
custom: it-pro
ms.openlocfilehash: 301e1b2ca45844422527a28fac278e5614e4f536
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52664322"
---
# <a name="what-is-azure-identity"></a>什么是 Azure 标识？
Azure Active Directory (Azure AD) 是一个标识和访问管理云解决方案，它提供目录服务、标识监管和应用程序访问管理功能。 

在创建 Azure 订阅时，单个 Azure AD 目录自动与其进行关联。 然后，作为 Azure 中的标识服务，Azure AD 为基于云的资源提供所有标识管理和访问控制功能。 这些资源可能包括个体租户（组织）的用户、应用和组，如下图中所示：

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Azure 根据每个组织的需求提供复杂程度不一的多种方式来利用标识即服务 (IDaaS)。 本文的余下部分将帮助你理解基本 Azure 标识术语和概念，并提供建议来帮助你从可用选项中做出最佳的选择。

## <a name="terms-to-know"></a>要了解的术语

在为组织做出 Azure 标识解决方案方面的决策之前，需要基本了解有关 Azure 标识服务的常用术语。

|要理解的术语| 说明|
|-----|-----|
|Azure 订阅 |订阅用于支付 Azure 云服务的费用，通常与信用卡相链接。 可以注册多个订阅，但可能难以在订阅之间共享资源。|
|Azure 租户 | 一个 Azure AD 租户代表一家组织。 它是组织在注册 Azure、Intune 或 Office 365 等 Azure 云服务订阅时自动创建的专用受信任 Azure AD 实例。 租户可以访问专用环境（单租户）中的服务，或者与其他组织共享的环境（多租户）中的服务。|
|Azure AD 目录 | 每个 Azure 租户都有一个专用的受信任 Azure AD 目录，其中包含该租户的用户、组和应用程序。 该目录用于针对租户资源执行标识和访问管理功能。 由于注册 Azure、Microsoft Intune 或 Office 365 等 Azure 云服务时会自动预配一个唯一的 Azure AD 目录来代表组织，因此，术语“租户”、“Azure AD”和“Azure AD 目录”有时会换用。 |
|自定义域 | 首次注册 Azure 云服务订阅时，你的租户（组织）使用 *.partner.onmschina.cn* 域名。 但是，大多数组织有一个或多个用于执行业务以及由最终用户用来访问公司资源的域名。 可以将自定义域名添加到 Azure AD，使用户能够轻松记住该域名，例如，使用 *alice@contoso.com* 而不要使用 *alice@contoso.partner.onmschina.cn*。 |
|Azure AD 帐户 | 这是使用 Azure AD 或其他 Azure 云服务（例如 Office 365）创建的标识。 它们存储在 Azure AD 中，可供组织的任何云服务订阅访问。 |
|Azure 订阅管理员| 帐户管理员是注册或购买 Azure 订阅的人员。 他们可以使用[帐户中心](https://account.windowsazure.cn/Subscriptions)来执行各种管理任务，例如创建订阅、取消订阅、更改订阅的计费信息，或者更改服务管理员。 |
|Azure AD 全局管理员 | Azure AD 全局管理员对所有 Azure AD 管理功能拥有完全访问权限。 默认情况下，注册 Azure 云服务订阅的人员将成为自动全局管理员。 可以分配多个全局管理员，但只有全局管理员才能向用户分配任何[其他管理员角色](/active-directory/active-directory-assign-admin-roles-azure-portal)。 |
|Microsoft 帐户 | Microsoft 帐户（由你创建供个人使用）提供对面向使用者的 Microsoft 产品和云服务（例如 Outlook (Hotmail)、OneDrive、Xbox LIVE 或 Office 365）的访问。 这些标识在 Microsoft 运行的 Microsoft 使用者标识帐户系统中创建和存储。|
|工作或学校帐户 | 工作或学校帐户（由管理员针对商业/学术用途颁发）提供对企业级 Azure 云服务（例如 Azure、Intune 或 Office 365）的访问。|


## <a name="concepts-to-understand"></a>要理解的概念

现在，你已了解了基本 Azure 标识术语，你应当更加详细地了解这些 Azure 标识概念以帮助你做出信息充分的 Azure 标识服务决策。

|要了解的概念 |说明|
|-----|-----|
|Azure 订阅与 Azure Active Directory 的关联方式 |每个 Azure 订阅都与 Azure AD 目录建立了信任关系，以便对用户、服务和设备进行身份验证。 *多个订阅可以信任同一个 Azure AD 目录，但订阅始终只会信任单个 Azure AD 目录*。 这种信任关系不同于订阅与其他 Azure 资源（网站、数据库等）之间的信任关系，在后一种关系中，这些资源更像是订阅的子资源。 如果某个订阅过期，则对该订阅关联的非 Azure AD 资源的访问权限也将终止。 但是，Azure AD 目录将保留在 Azure 中，并且你可以将另一个订阅与该目录相关联，然后继续管理租户资源。|
|[Azure 门户中基于角色的访问控制](/role-based-access-control/overview)|Azure 基于角色的访问控制 (RBAC) 可帮助为 Azure 资源提供精细的访问管理。 权限过多，可能会向攻击者公开帐户。 权限太少，员工无法有效完成其工作。 使用 RBAC，可以根据应用到所有资源组的以下三个基本角色为员工分配所需的确切权限：所有者、参与者、读取者。 此外，还可以根据具体的需求，最多创建 2,000 个自己的[自定义 RBAC 角色](/role-based-access-control/custom-roles)。 |


### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Windows Server AD DS 与 Azure AD 之间的差别
Azure Active Directory (Azure AD) 和本地 Active Directory（Active Directory 域服务或 AD DS）都是存储目录数据和管理用户和资源之间通信（包括用户登录过程、身份验证和目录搜索）的系统。

如果你已熟悉 Windows 2000 Server 中最先引入的本地 Windows Server Active Directory 域服务 (AD DS) 的话，则可能已了解标识服务的基本概念。 但是，还要了解的一个要点是，Azure AD 不仅仅是在云中的域控制器。 它是在 Azure 中提供标识即服务 (IDaaS) 的一种全新方式，让我们以完全不同的思路考虑如何全面融入基于云的功能，以及帮助组织抵御新型威胁。 

AD DS 是 Windows Server 上的服务器角色，这意味着可将它部署在物理计算机或虚拟机上。 它的层次结构基于 X.500。 它使用 DNS 来查找对象，可以使用 LDAP 进行交互，且主要使用 Kerberos 进行身份验证。 除了将计算机加入域之外，Active Directory 还启用组织单位 (OU) 和组策略对象 (GPO)，并在域之间创建信任。

多年以来，IT 部门一直在使用 AD DS 保护其安全外围，但支持员工、客户和合作伙伴标识需求的新式无外围企业需要一个新的控制平面。 Azure AD 就是这样一个标识控制平面。 安全保护已从企业防火墙延伸到云中，Azure AD 可在其中通过为用户提供一个通用标识来保护公司资源和访问（在本地或云中）。 这为用户提供了灵活性来安全地访问他们所需的应用，可以从几乎任何设备完成其工作。 此外，还提供以机器学习功能和深度报告为后盾的、IT 部门为保护数据安全而需要的基于风险的无缝数据保护控制机制。

Azure AD 是多客户公共目录服务，这意味着用户可以在 Azure AD 中为云服务器和 Office 365 之类的应用程序创建租户。 用户和组在平面结构中创建，没有 OU 或 GPO。 身份验证通过 SAML、WS-Federation、OAuth 等协议执行。 可以查询 Azure AD，但必须使用名为“AD Graph API”的 REST API，而不能使用 LDAP。 这些都是基于 HTTP 和 HTTPS 运行的。

### <a name="extend-office-365-management-and-security-capabilities"></a>扩展 Office 365 管理和安全功能
已在使用 Office 365？ 使用 Azure AD 扩展内置的 Office 365 功能来保护所有资源，使整个工作队伍能够安全地工作，可以加速数字转换。 使用 Azure AD 时，除了 Office 365 功能以外，还可以使用一个实现所有应用的单一登录的标识保护整个应用程序产品组合。 在需要时，多重身份验证 (MFA) 功能将提供更大的保护。 可以获取用户特权的其他见解，并提供按需、适时的管理访问。 得益于 Azure AD 提供的自助服务功能，例如重置忘记的密码、应用程序访问请求，以及创建和管理组，用户的工作效率将会提高，创建的技术支持票证将会减少。

> [!TIP]
> 想要详细了解如何在 Office 365 中使用 Azure AD 标识管理？ [获取电子书](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html)。


## <a name="common-scenarios-and-recommendations"></a>常见方案和建议

下面是一些常见的标识和访问方案，其中每个方案都包含有关最适合的 Azure 标识选项的建议。

|标识方案| 建议|
|-----|-----|
|我的业务立足于云，我们未投资购置任何本地标识解决方案。| 对于只在云中开展业务，对本地解决方案未做任何投资的企业而言，[Azure Active Directory](/active-directory/active-directory-whatis) 是最佳选择。|

## <a name="where-can-i-learn-more"></a>可以从何处了解详细信息？
我们提供了大量的优秀在线资源，以帮助用户了解 Azure AD 的方方面面。 下面是一些有助于快速入门的优秀文章：

- [使用 Azure AD Connect 启用目录的混合管理](../hybrid/whatis-hybrid-identity.md)
- [在互联世界中提高安全性](../authentication/multi-factor-authentication.md)
- [从任意位置管理密码](../user-help/active-directory-passwords-update-your-own-password.md)
- [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)

## <a name="next-steps"></a>后续步骤

了解 Azure 标识的概念以及可用的选项后，可以使用以下资源开始实现所选的选项：


[详细了解 Azure 概念证明环境](https://aka.ms/aad-poc)

<!-- Update_Description: link update -->