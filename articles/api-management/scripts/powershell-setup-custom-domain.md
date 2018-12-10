---
title: Azure PowerShell 脚本示例 - 设置自定义域
description: Azure PowerShell 脚本示例 - 设置自定义域
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
origin.date: 12/14/2017
ms.date: 08/13/2018
ms.author: v-yiso
ms.custom: mvc
ms.openlocfilehash: aa9636212b4b296d15838e4a7a0753decfa858b9
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643759"
---
# <a name="set-up-custom-domain"></a>设置自定义域

此示例脚本在 API 管理服务的代理和门户终结点上设置自定义域。


如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount -Environment AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell
##########################################################
#  Script to setup custom domain on proxy and portal endpoint
#  of api management service.
###########################################################

$random = (New-Guid).ToString().Substring(0,8)

#Azure specific details
$subscriptionId = "my-azure-subscription-id"

# Api Management service specific details
$apimServiceName = "apim-$random"
$resourceGroupName = "apim-rg-$random"
$location = "China East"
$organisation = "Contoso"
$adminEmail = "admin@contoso.com"

# Set the context to the subscription Id where the cluster will be created
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a resource group.
New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

# Create the Api Management service. Since the SKU is not specified, it creates a service with Developer SKU. 
New-AzureRmApiManagement -ResourceGroupName $resourceGroupName -Name $apimServiceName -Location $location -Organization $organisation -AdminEmail $adminEmail

# Certificate related details
$proxyHostname = "proxy.contoso.net"
# Certificate containing Common Name CN="proxy.contoso.net" or CN=*.contoso.net
$proxyCertificatePath = "C:\proxycert.pfx"
$proxyCertificatePassword = "certPassword"

$portalHostname = "portal.contoso.net"
# Certificate containing Common Name CN="portal.contoso.net" or CN=*.contoso.net
$portalCertificatePath = "C:\portalcert.pfx"
$portalCertificatePassword = "certPassword"

# Upload the custom ssl certificate to be applied to Proxy endpoint / Api Gateway endpoint
$proxyCertUploadResult = Import-AzureRmApiManagementHostnameCertificate -Name $apimServiceName -ResourceGroupName $resourceGroupName `
                        -HostnameType "Proxy" -PfxPath $proxyCertificatePath -PfxPassword $proxyCertificatePassword

# Upload the custom ssl certificate to be applied to Portal endpoint
$portalCertUploadResult = Import-AzureRmApiManagementHostnameCertificate -Name $apimServiceName -ResourceGroupName $resourceGroupName `
                        -HostnameType "Portal" -PfxPath $portalCertificatePath -PfxPassword $portalCertificatePassword

# Create the HostnameConfiguration object for Portal endpoint
$PortalHostnameConf = New-AzureRmApiManagementHostnameConfiguration -Hostname $proxyHostname -CertificateThumbprint $proxyCertUploadResult.Thumbprint

# Create the HostnameConfiguration object for Proxy endpoint
$ProxyHostnameConf = New-AzureRmApiManagementHostnameConfiguration -Hostname $portalHostname -CertificateThumbprint $portalCertUploadResult.Thumbprint

# Apply the configuration to API Management
Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resourceGroupName `
        -PortalHostnameConfiguration $PortalHostnameConf -ProxyHostnameConfiguration $ProxyHostnameConf
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

[!INCLUDE [api-management-custom-domain](../../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [PowerShell 示例](../powershell-samples.md)中找到 Azure API 管理的其他 Azure Powershell 示例。