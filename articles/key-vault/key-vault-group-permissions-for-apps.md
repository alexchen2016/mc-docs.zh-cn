---
title: 向多个应用程序授予对 Azure 密钥保管库的访问权限 - Azure 密钥保管库 | Azure Docs
description: 了解如何为多个应用程序授予密钥保管库的访问权限
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/01/2016
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 5bd36e0e0feef2345185269280ae4bffa822df31
ms.sourcegitcommit: df1adc5cce721db439c1a7af67f1b19280004b2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63844723"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>授予多个应用程序访问密钥保管库的权限

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

访问控制策略可用于授予多个应用程序访问密钥保管库的权限。 访问控制策略最多可支持 1024 个应用程序，且其配置过程如下：

1. 创建一个 Azure Active Directory 安全组。 
2. 将应用程序的所有关联服务主体添加到安全组。
3. 授予安全组访问 Key Vault 的权限。

## <a name="prerequisites"></a>先决条件

以下是先决条件：
* [安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
* [安装 Azure Active Directory V2 PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD)。
* 在 Azure Active Directory 租户中创建/编辑组的权限。 如果没有权限，则可能需要与 Azure Active Directory 管理员联系。 有关 Key Vault 访问策略权限的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](about-keys-secrets-and-certificates.md)。

## <a name="granting-key-vault-access-to-applications"></a>授予对应用程序的 Key Vault 访问权限

在 PowerShell 中运行以下命令：

```powershell
# Connect to Azure AD 
Connect-AzureAD -AzureEnvironment AzureChinaCloud 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

如果需要为一组应用程序授予一组不同的权限，请为此类应用程序创建单独的 Azure Active Directory 安全组。

## <a name="next-steps"></a>后续步骤

深入了解如何[保护密钥保管库](key-vault-secure-your-key-vault.md)。
