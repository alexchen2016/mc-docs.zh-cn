---
title: Azure PowerShell 脚本示例 - 将应用连接到 SQL 数据库 | Azure Docs
description: Azure PowerShell 脚本示例 - 将应用服务应用连接到 SQL 数据库
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 03/20/2017
ms.date: 03/18/2019
ms.author: v-biyu
ms.custom: mvc
ms.openlocfilehash: 4debb307de2cfe623e05ffdfaf9d16ca487dc4e7
ms.sourcegitcommit: 0ccbf718e90bc4e374df83b1460585d3b17239ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57347221"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>将应用服务应用连接到 SQL 数据库

在此方案中，将了解如何创建 Azure SQL 数据库和应用服务应用。 然后，将使用应用设置将 SQL 数据库链接到应用。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount -EnvironmentName AzureChinaCloud` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
```powershell
# Generates a Random Value
$Random=(New-Guid).ToString().Substring(0,8)

# Variables
$ResourceGroup="MyResourceGroup$Random"
$AppName="webappwithSQL$Random"
$Location="China North"
$ServerName="webappwithsql$Random"
$StartIP="0.0.0.0"
$EndIP="0.0.0.0"
$Username="ServerAdmin"
$Password="<provide-a-password>"
$SqlServerPassword=New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $Username,(ConvertTo-SecureString -String $Password -AsPlainText -Force)

# Create a Resource Group
New-AzResourceGroup -Name $ResourceGroup -Location $Location

# Create an App Service Plan
New-AzAppservicePlan -Name WebAppwithSQLPlan -ResourceGroupName $ResourceGroup -Location $Location -Tier Basic

# Create a Web App in the App Service Plan
New-AzWebApp -Name $AppName -ResourceGroupName $ResourceGroup -Location $Location -AppServicePlan WebAppwithSQLPlan

# Create a SQL Database Server
New-AzSQLServer -ServerName $ServerName -Location $Location -SqlAdministratorCredentials $SqlServerPassword -ResourceGroupName $ResourceGroup

# Create Firewall Rule for SQL Database Server
New-AzSqlServerFirewallRule -FirewallRuleName "AllowYourIp" -StartIpAddress $StartIP -EndIPAddress $EndIP -ServerName $ServerName -ResourceGroupName $ResourceGroup

# Create SQL Database in SQL Database Server
New-AzSQLDatabase -ServerName $ServerName -DatabaseName MySampleDatabase -ResourceGroupName $ResourceGroup

# Assign Connection String to Connection String 
Set-AzWebApp -ConnectionStrings @{ MyConnectionString = @{ Type="SQLAzure"; Value ="Server=tcp:$ServerName.database.windows.net;Database=MySampleDatabase;User ID=$Username@$ServerName;Password=$password;Trusted_Connection=False;Encrypt=True;" } } -Name $AppName -ResourceGroupName $ResourceGroup

```

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、应用服务应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](https://docs.microsoft.com/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](https://docs.microsoft.com/powershell/module/az.websites/new-azwebapp) | 创建应用服务应用。 |
| [New-AzSQLServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) | 创建 SQL 数据库服务器。 |
| [New-AzSqlServerFirewallRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为 SQL 数据库服务器创建防火墙规则。 |
| [New-AzSQLDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase) | 创建数据库或弹性数据库。 |
| [Set-AzWebApp](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) | 修改应用服务应用的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务的其他 Azure Powershell 示例。
