---
title: Azure Stack MySQL 资源提供程序 1.1.30.0 发行说明 | Microsoft Docs
description: 了解 Azure Stack MySQL 资源提供程序最新更新版的信息，包括任何已知问题和下载位置。
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
ms.topic: article
origin.date: 01/09/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: aad3d0c1ec2433be3a86a56e32cc4fc770579b89
ms.sourcegitcommit: 05aa4e4870839a3145c1a3835b88cf5279ea9b32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64529521"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL 资源提供程序 1.1.33.0 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本发行说明描述 MySQL 资源提供程序 1.1.33.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 下面列出了安装此 MySQL 资源提供程序版本所需的最低 Azure Stack 发行版：

> |最低 Azure Stack 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |版本 1808 (1.1808.0.97)|[MySQL RP 版本 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，请先将支持的最低 Azure Stack 更新版应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修复
此 Azure Stack MySQL 资源提供程序版本包含以下改进和修复：

### <a name="fixes"></a>修复项
- **MySQL 资源提供程序门户扩展可能选择了错误的订阅**。 MySQL 资源提供程序使用 Azure 资源管理器调用来确定要使用的第一个服务管理员订阅，该订阅可能不是默认的提供程序订阅。 如果发生这种情况，则表明 MySQL 资源提供程序运行不正常。 

- **MySQL 宿主服务器未列出托管的数据库。** 查看 MySQL 宿主服务器的租户资源时，可能会发现用户创建的数据库未列出。

- **如果 TLS 1.2 未启用，以前的 MySQL 资源提供程序 (1.1.30.0) 部署可能会失败**。 更新了 MySQL 资源提供程序 1.1.33.0，可以在部署资源提供程序、更新资源提供程序或轮换机密时启用 TLS 1.2。 

- **MySQL 资源提供程序机密轮换失败**。 修复了在轮换机密时会生成以下错误代码的问题：` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>已知问题 

- **MySQL SKU 最长可能需要在一小时后才显示在门户中**。 创建新的 MySQL 数据库时，新建的 SKU 最长可能需要在一小时后才显示出来并可供使用。 

    **解决方法**：无。

- **重复使用 MySQL 登录名**。 尝试使用与现有登录名相同的用户名在同一订阅下创建新的 MySQL 登录名时，会导致重复使用同一登录名和现有密码的问题。 

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或者在不同订阅下使用相同的用户名创建登录名。

- **共享的 MySQL 登录名导致数据不一致**。 如果在同一订阅下为多个 MySQL 数据库共享某个 MySQL 登录名，则更改登录密码会导致数据不一致。

    **解决方法**：在同一订阅下始终对不同的数据库使用不同的登录名。


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>云管理员操作 Azure Stack 时的已知问题
请参阅 [Azure Stack 发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[详细了解 MySQL 资源提供程序](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从旧版升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 