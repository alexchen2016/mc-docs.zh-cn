---
title: Azure PowerShell 脚本示例 - 将应用程序证书添加到群集 | Azure
description: Azure PowerShell 脚本示例 - 将应用程序证书添加到 Service Fabric 群集。
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 01/18/2018
ms.date: 05/28/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 4c257accb7dda4e94c47631fbe72fb4103ca5f9f
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52650691"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>将应用程序证书添加到 Service Fabric 群集

此示例脚本在指定的 Azure Key Vault 中创建一个自签名证书，并将它安装到 Service Fabric 群集的所有节点上。 该证书还会下载到本地文件夹。 已下载证书的名称与 Key Vault 中证书的名称相同。 根据需要自定义参数。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzureRmAccount -Environment AzureChinaCloud ` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

```powershell

# Variables for common values.
$clusterloc="chinaeast"
$groupname="mysfclustergroup"
$clustername = "mysfcluster"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.chinacloudapi.cn"

# Certificate variables.
$appcertpwd = ConvertTo-SecureString -String 'Password#1234' -AsPlainText -Force
$appcertfolder="c:\myappcertificates\"

# Create a new self-signed certificate and add it to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername `
    -KeyVaultName $vaultname -KeyVaultResouceGroupName $groupname -CertificateSubjectName $subname `
    -CertificateOutputFolder $appcertfolder -CertificatePassword $appcertpwd
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | 将新的应用程序证书添加到构成群集的虚拟机规模集。  |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。
<!--Update_Description: update meta properties, update link, wording update -->