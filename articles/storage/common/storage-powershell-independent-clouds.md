---
title: 使用 Azure PowerShell 管理 Azure 独立云中的存储 | Microsoft Docs
description: 使用 Azure PowerShell 管理中国云、政府云和德国云中的存储
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/24/2017
ms.date: 05/27/2019
ms.author: v-jay
ms.subservice: common
ms.openlocfilehash: 4f784ddb6972e843cfbd6862eefa07ea08fded3c
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004030"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>使用 PowerShell 管理 Azure 独立云中的存储

大多数人为其全球 Azure 部署使用了 Azure 公有云。 但出于主权等方面的原因，还存在一些独立的 Microsoft Azure 部署。 这些独立部署称为“环境”。 以下列表详细说明了当前存在的独立云。

* [Azure 政府云](https://azure.microsoft.com/features/gov/)
* [由中国世纪互联运营的 Azure 中国云](http://www.windowsazure.cn/)
* Azure 德国云

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>使用独立云 

若要在某个独立云中使用 Azure 存储，需要连接到该云而不是 Azure 公有云。 若要使用某个独立云而不是 Azure 公有云，需要：

* 指定要连接到的环境。 
* 确定并使用可用的区域。
* 使用正确的终结点后缀，它不同于 Azure 公有云。

本文中的示例需要 Azure PowerShell 模块 Az 版本 0.7 或更高版本。 在 PowerShell 窗口中，运行 `Get-Module -ListAvailable Az` 可查找版本。 如果未列出任何信息或需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)。 

## <a name="log-in-to-azure"></a>登录 Azure

运行 [Get-AzEnvironment](https://docs.microsoft.com/powershell/module/az.accounts/get-azenvironment) cmdlet 以查看可用的 Azure 环境：
   
```powershell
Get-AzEnvironment
```

登录到有权访问所要连接的云的帐户，并设置环境。 此示例演示如何登录到使用 Azure 中国云的帐户。   

```powershell
Connect-AzAccount –Environment AzureChinaCloud
```


此时，如果需要查看可在其中创建存储帐户或其他资源的位置列表，可以使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) 查询所选云可用的位置。

```powershell
Get-AzLocation | select Location, DisplayName
```

下表显示了针对中国云返回的位置。

|位置 | DisplayName |
|----|----|
| chinaeast |  中国东部 |
| chinaeast 2 | 中国东部 2 |
| chinanorth | 中国北部 |
| chinanorth 2 | 中国北部 2 |


## <a name="endpoint-suffix"></a>终结点后缀

其中每个环境的终结点后缀不同于 Azure 公有云终结点。 例如，Azure 公有云的 Blob 终结点后缀为 **blob.core.windows.net**。 对于中国云，Blob 终结点后缀为 **blob.core.chinacloudapi.cn**。 

### <a name="get-endpoint-using-get-azenvironment"></a>使用 Get-AzEnvironment 获取终结点 

使用 [Get-AzEnvironment](https://docs.microsoft.com/powershell/module/az.accounts/get-azenvironment) 检索终结点后缀。 终结点是环境的 *StorageEndpointSuffix* 属性。 以下代码片段演示了如何执行此操作。 所有这些命令返回类似于“core.chinacloudapi.cn”的内容。 将此内容追加到存储服务即可访问该服务。 例如，追加“queue.core.chinacloudapi.cn”可访问中国云中的队列服务。

此代码片段检索所有环境，以及每个环境的终结点后缀。

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

此命令返回以下结果。

| Name| core.usgovcloudapi.net|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

若要检索指定环境的所有属性，请调用 Get-AzEnvironment 并指定云名称  。 此代码片段返回属性列表；请在列表中查找 **StorageEndpointSuffix**。 以下示例适用于中国云。

```powershell
Get-AzEnvironment -Name AzureChinaCloud 
```

结果如下所示：

|属性名称|Value|
|----|----|
| Name | AzureChinaCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | https://management.core.chinacloudapi.cn/ |
| GalleryURL | https://gallery.azure.com/ |
| ManagementPortalUrl | http://go.microsoft.com/fwlink/?LinkId=301902 | 
| ServiceManagementUrl | https://management.core.chinacloudapi.cn/ |
| PublishSettingsFileUrl| http://go.microsoft.com/fwlink/?LinkID=301776 |
| ResourceManagerUrl | https://management.chinacloudapi.cn/ |
| SqlDatabaseDnsSuffix | .database.chinacloudapi.cn |
| **StorageEndpointSuffix** | core.chinacloudapi.cn |
| ... | ... | 

若只要检索存储终结点后缀属性，请检索特定的云，并仅请求该属性。

```powershell
$environment = Get-AzEnvironment -Name AzureChinaCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

此命令返回以下信息。

```
Storage Endpoint Suffix = core.chinacloudapi.cn
```

### <a name="get-endpoint-from-a-storage-account"></a>从存储帐户获取终结点

还可以通过检查存储帐户的属性来检索终结点。 如果已在 PowerShell 脚本中使用存储帐户，则这种方法很有帮助；可以做到只检索所需的终结点。 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

对于中国云中的存储帐户，此命令返回以下信息： 

```
blob endpoint = http://myexistingstorageaccount.blob.core.chinacloudapi.cn/
file endpoint = http://myexistingstorageaccount.file.core.chinacloudapi.cn/
queue endpoint = http://myexistingstorageaccount.queue.core.chinacloudapi.cn/
table endpoint = http://myexistingstorageaccount.table.core.chinacloudapi.cn/
```

## <a name="after-setting-the-environment"></a>设置环境之后

从现在起，可以根据[对 Azure 存储使用 Azure PowerShell](storage-powershell-guide-full.md) 一文中所述，使用管理存储帐户时所用的相同 PowerShell 以及访问数据平面。

## <a name="clean-up-resources"></a>清理资源

如果在本练习中创建了新的资源组和存储帐户，可以通过删除资源组来删除所有资产。 这会一并删除组中包含的所有资源。 在这种情况下，它会删除创建的存储帐户以及资源组本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

* [在不同的 PowerShell 会话中保留用户登录](https://docs.microsoft.com/powershell/azure/context-persistence)
* [Azure 中国版应用程序开发人员说明](https://msdn.microsoft.com/library/azure/dn578439.aspx)