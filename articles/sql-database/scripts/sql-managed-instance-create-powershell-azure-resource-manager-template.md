---
title: PowerShell 示例 - 在 Azure SQL 数据库中创建托管实例 | Microsoft Docs
description: Azure PowerShell 示例脚本，用于在 Azure SQL 数据库中创建托管实例
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 03/12/2019
ms.date: 04/29/2019
ms.openlocfilehash: 3233b7549a7ea9af2ce6bd1e91289f9980c3d0ac
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854723"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>将 PowerShell 与 Azure 资源管理器模板配合使用，以在 Azure SQL 数据库中创建托管实例

可以使用 Azure PowerShell 库和 Azure 资源管理器模板来创建 Azure SQL 数据库托管实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

Azure PowerShell 命令可以使用预定义的 Azure 资源管理器模板启动部署。 可以在模板中指定以下属性：

- 实例名称
- SQL 管理员用户名和密码。
- 实例的大小（内核数和最大存储大小）。
- 要将实例放置在其中的 VNet 和子网。
- 实例的服务器级排序规则（预览版）。

实例名称、SQL 管理员用户名、VNet/子网和排序规则以后无法更改。 其他实例属性可以更改。

## <a name="prerequisites"></a>先决条件

此示例假定已为托管实例[创建了有效的网络环境](../sql-database-managed-instance-create-vnet-subnet.md)或[修改了现有的 VNet](../sql-database-managed-instance-configure-vnet-subnet.md)。 此示例使用 cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)，因此请确保已安装以下 PowerShell 模块：

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

应当在表示模板的文件中放置将用来创建实例的以下内容：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "China East",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

我们假设已存在正确配置了子网的 Azure VNet。 如果没有正确配置的子网，请使用可以独立执行或包括在此模板中的单独 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)来准备网络环境。

将此文件的内容保存为 .json 文件，将该文件路径置于以下 PowerShell 脚本中，并在脚本中更改对象的名称：

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "China East"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

成功运行该脚本后，可以通过所有 Azure 服务和配置的 IP 地址访问 SQL 数据库。

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
