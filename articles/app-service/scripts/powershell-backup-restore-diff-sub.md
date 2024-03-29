---
title: Azure PowerShell 脚本示例 - 将应用备份还原到其他订阅 | Azure Docs
description: Azure PowerShell 脚本示例 - 从另一订阅的备份中还原 Web 应用
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
origin.date: 11/21/2018
ms.date: 03/18/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: f42b0e6341dd6d2db8a92cf78510104820006774
ms.sourcegitcommit: 0ccbf718e90bc4e374df83b1460585d3b17239ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57347092"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>使用 PowerShell 从另一订阅中的备份还原 Web 应用

此示例脚本从现有的 Web 应用中检索以前已完成的备份，然后将其还原到另一订阅中的 Web 应用。 

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/en-us/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
```powershell
$resourceGroupNameSub1 = "<replace-with-your-group-name>"
$resourceGroupNameSub2 = "<replace-with-desired-new-group-name>"
$webAppNameSub1 = "<replace-with-your-app-name>"
$webAppNameSub2 = "<replace-with-desired-new-app-name>"
$appServicePlanSub2 = "<replace-with-desired-new-plan-name>"
$locationSub2 = "China North"


# Log into the subscription with the backup
Add-AzAccount

# List statuses of all backups that are complete or currently executing.
Get-AzWebAppBackupList -ResourceGroupName $resourceGroupNameSub1 -Name $webAppNameSub1

# Note the BackupID property of the backup you want to restore

# Get the backup object that you want to restore by specifying the BackupID
$backup = (Get-AzWebAppBackupList -ResourceGroupName $resourceGroupNameSub1 -Name $webAppNameSub1 | where {$_.BackupId -eq <replace-with-BackupID>}) 

# Log into the subscription that you want to restore the app to
Add-AzAccount

# Create a new web app
New-AzWebApp -ResourceGroupName $resourceGroupNameSub2 -AppServicePlan $appServicePlanSub2 -Name $webAppNameSub2 -Location $locationSub2

# Restore the app by overwriting it with the backup data
Restore-AzWebAppBackup -ResourceGroupName $resourceGroupNameSub2 -Name $webAppNameSub2 -StorageAccountUrl $backup.StorageAccountUrl -BlobName $backup.BlobName -Overwrite
```
## <a name="clean-up-deployment"></a>清理部署 

如果不再需要 Web 应用，请使用以下命令删除资源组、Web 应用和所有相关的资源。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [Add-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) | 添加一个经身份验证的帐户，用于 Azure 资源管理器 cmdlet 请求。  |
| [Get-AzWebAppBackupList](https://docs.microsoft.com/powershell/module/az.websites/get-azwebappbackuplist) | 获取 Web 应用的备份列表。 |
| [New-AzWebApp](https://docs.microsoft.com/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用 |
| [Restore-AzWebAppBackup](https://docs.microsoft.com/powershell/module/az.websites/restore-azwebappbackup) | 从以前完成的备份中还原 Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/en-us/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
