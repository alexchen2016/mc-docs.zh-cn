---
title: Azure AD Connect：选择安装类型 | Microsoft 文档
description: 本主题逐步讲解如何选择 Azure AD Connect 使用的安装类型
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/09/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: 18b481cccca9b50aef94cd5bcb64a8c91631cfbd
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52644790"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>选择用于 Azure AD Connect 的安装类型
进行全新安装时，AD Connect 有两种安装类型：快速安装和自定义安装。 本主题有助于用户确定安装过程中使用的具体选项。

## <a name="express"></a>Express
“快速”安装是最常用的选项，在所有全新安装中，大约 90% 会使用此选项。 快速安装旨在提供适合最常用客户方案的配置。

此安装假设：

- 用户在本地使用单个 Active Directory 林。
- 用户具有安装时能够使用的企业管理员帐户。
- 用户在本地 Active Directory 中的对象不到 100,000 个。

用户可以获得：

- 建立从本地到 Azure AD 的[密码哈希同步](how-to-connect-password-hash-synchronization.md)，实现单一登录。
- 可同步[用户、组、联系人和 Windows 10 计算机](concept-azure-ad-connect-sync-default-configuration.md)的配置。
- 同步所有域和所有 OU 中所有符合条件的对象。
- 启用[自动升级](how-to-connect-install-automatic-upgrade.md)，确保始终使用最新的可用版本。

仍可使用“快速”选项的场合：

- 如果不想要同步所有 OU，仍可使用“快速”选项。请在最后一页上取消选择“启动同步过程...”\*。 然后再次运行安装向导，更改[配置选项](how-to-connect-installation-wizard.md#customize-synchronization-options)中的 OU 并启用计划同步。

## <a name="custom"></a>“自定义”
与“快速”安装相比，自定义的路径允许更多选项。 此安装适用于上述针对“快速”安装的配置对组织来说不具代表性的所有情况。

使用时机：

- 无法访问 Active Directory 中的企业管理员帐户。
- 有多个林，或者计划在将来同步多个林。
- 林中的域无法从 Connect 服务器访问。
- 计划使用联合身份验证或直通身份验证进行用户登录。
- 有 100,000 多个对象，需使用整个 SQL Server。
- 计划使用基于组的筛选，而不是只基于域或 OU 的筛选。

## <a name="upgrade-from-dirsync"></a>从 DirSync 升级
如果当前正在使用 DirSync，请遵循[从 DirSync 升级](how-to-dirsync-upgrade-get-started.md)中的步骤升级现有配置。 有两个不同的升级选项：

- 就地升级，用于在同一服务器上安装 Connect。
- 并行部署，用于在新服务器上安装 Connect，此时现有的 DirSync 服务器仍可正常运行。

## <a name="upgrade-from-azure-ad-sync"></a>从 Azure AD Sync 升级
如果当前正在使用 Azure AD Sync，可以遵循从一个 Connect 版本升级到更新版本时采用的[相同步骤](how-to-upgrade-previous-version.md)。 有两个不同的升级选项：

- 就地升级，用于在同一服务器上安装 Connect。
- 交叉迁移，用于在新服务器上安装 Connect，此时现有的 Azure AD Sync 服务器仍可正常运行。

## <a name="migrate-from-fim2010-or-mim2016"></a>从 FIM2010 或 MIM2016 迁移
如果目前是在将 Forefront Identity Manager 2010 或 Microsoft Identity Manager 2016 与 Azure AD 连接器结合使用，则唯一选项是迁移。 请遵循[交叉迁移](how-to-upgrade-previous-version.md#swing-migration)中所述的步骤。 执行这些步骤时，请将出现的所有 Azure AD Sync 替换为 FIM2010/MIM2016。

## <a name="next-steps"></a>后续步骤
根据已选定使用的选项，使用左侧的目录查找包含详细步骤的文章。

