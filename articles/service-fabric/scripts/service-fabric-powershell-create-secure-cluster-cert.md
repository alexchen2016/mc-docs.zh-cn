---
title: Azure PowerShell 脚本示例 - 创建 Service Fabric 群集 | Azure
description: Azure PowerShell 脚本示例 - 创建 Service Fabric 群集。
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 01/19/2018
ms.date: 05/28/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 398b80a7d01d055d6c75cfe43b3e44f67114d278
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52653746"
---
# <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

此示例脚本将创建一个由五个节点组成的 Service Fabric 群集（使用 X.509 证书保护的群集）。  该命令将创建一个自签名证书，并将其上传到新的 Key Vault。 该证书也会复制到本地目录。  设置 *-OS* 参数可选择群集节点上运行的 Windows 或 Linux 的版本。  根据需要自定义参数。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzureRmAccount -Environment AzureChinaCloud ` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

```powershell
#Provide the subscription Id
$subscriptionId = 'yourSubscriptionId'

# Certificate variables.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"

# Variables for VM admin.
$adminuser="vmadmin"
$adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

# Variables for common values
$clusterloc="chinaeast"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.chinacloudapi.cn"

# Set the number of cluster nodes. Possible values: 1, 3-99
$clustersize=5 

# Set the context to the subscription Id where the cluster will be created
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、群集以及所有相关资源。

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmServiceFabricCluster](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 新建 Service Fabric 群集。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。
<!--Update_Description: update meta properties, wording update, wording update -->