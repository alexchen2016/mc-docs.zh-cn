---
title: Azure PowerShell 脚本 - 创建 Azure Cosmos DB 故障转移策略
description: Azure PowerShell 脚本示例 - 创建 Azure Cosmos DB 故障转移策略
ms.service: cosmos-db
author: rockboyfor
ms.author: v-yeche
ms.devlang: PowerShell
ms.subservice: cosmosdb-sql
ms.topic: sample
origin.date: 05/10/2017
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: ca561dfe49e25898cedfd3250cd42dd2f152841b
ms.sourcegitcommit: f85e05861148b480d6c9ea95ce84a17145872442
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615261"
---
# <a name="create-an-azure-cosmos-db-failover-policy-for-high-availability-using-powershell"></a>使用 PowerShell 创建 Azure Cosmos DB 故障转移策略以实现高可用性

此示例 PowerShell 脚本创建 Azure Cosmos DB 的故障转移策略以实现高可用性。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroup"
$resourceGroupLocation = "chinaeast"

# Create the resource group
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Database name
$DBName = "testdb"

# Write and read locations and priorities for the database
$locations = @(@{"locationName"="chinaeast"; 
                 "failoverPriority"=0}, 
               @{"locationName"="chinanorth"; 
                  "failoverPriority"=1})

# Consistency policy
$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard"; 
                          "locations"=$locations; 
                          "consistencyPolicy"=$consistencyPolicy}

# Create the database
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

# Reverse priorities
$failoverPolicies = @(@{"locationName"="chinaeast"; 
                        "failoverPriority"=1},
                      @{"locationName"="chinanorth"; 
                        "failoverPriority"=0})

# Update an existing database with the failover policies
Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName "<resource-group-name>" `
    -Name "<database-account-name>" `
    -Parameters @{"failoverPolicies"=$failoverPolicies}

```
<!--Notice: failoverPriority = 1 and 0 with reverse-->

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | 对 Azure CosmosDB 帐户调用某个操作。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。

<!-- Update_Description: update meta properties, wording update -->