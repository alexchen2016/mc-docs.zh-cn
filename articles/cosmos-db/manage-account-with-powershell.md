---
title: 使用 PowerShell 创建和管理 Azure Cosmos DB 资源
description: 使用 Azure Powershell 管理 Azure Cosmos DB 帐户。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 04/15/2019
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: a3ecb9f3b7cfb1d802f6bc5d964265bcd4463766
ms.sourcegitcommit: f85e05861148b480d6c9ea95ce84a17145872442
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615237"
---
# <a name="manage-azure-cosmos-resources-using-powershell"></a>使用 PowerShell 管理 Azure Cosmos 资源

以下指南介绍了使用 Azure Powershell 自动管理 Azure Cosmos DB 数据库帐户的命令。 它还包括用于管理 [多区域数据库帐户][distribute-data-multiple-regionally]的帐户密钥和故障转移优先级的命令。 更新数据库帐户可以修改一致性策略以及添加/删除区域。 对于 Azure Cosmos DB 帐户的跨平台管理，可使用 [Azure CLI](cli-samples.md)、[资源提供程序 REST API][rp-rest-api] 或 [Azure 门户](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>入门

请按照[如何安装和配置 Azure PowerShell][powershell-install-configure] 中的说明在 PowerShell 中安装 Azure Resource Manager 帐户并登录到该帐户。

### <a name="notes"></a>注释

* 如果想要执行以下命令而无需用户确认，请向命令追加 `-Force` 标志。
* 以下所有命令都是同步执行的。

<a name="create-documentdb-account-powershell"></a>
##  <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用此命令可创建 Azure Cosmos DB 数据库帐户。 可以将新数据库帐户配置为具有特定[一致性策略](consistency-levels.md)的单区域或[多区域][distribute-data-multiple-regionally]。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties

* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值需要为 0。 每个数据库帐户必须有且只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值需要大于 0。 每个数据库帐户可以有多个读取区域。
* `<ip-range-filter>` 指定 IP 地址集合或者 CIDR 格式的 IP 地址范围，以便将这些地址作为指定数据库帐户所允许的客户端 IP 列表。 IP 地址/范围必须以逗号分隔，且不得包含空格。 有关详细信息，请参阅 [Azure Cosmos DB 防火墙支持](firewall-support.md)
* `<default-consistency-level>` Azure Cosmos DB 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `<max-interval>` 与有限过期一致性一起使用时，此值表示允许的过期时间（以秒为单位）。 此值的接受范围为 1-100。
* `<max-staleness-prefix>` 与有限过期一致性一起使用时，此值表示允许的过期请求数。 此值的接受范围为 1 - 2,147,483,647。
* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<resource-group-location>` 新的 Azure Cosmos DB 数据库帐户所属的 Azure 资源组的位置。
* `<database-account-name>` 要创建的 Azure Cosmos DB 数据库帐户的名称。 它只能使用小写字母、数字及“-”字符，且长度必须为 3 到 50 个字符。

示例： 

    $locations = @(@{"locationName"="China North"; "failoverPriority"=0}, @{"locationName"="China East"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "China North" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>注释
* 上述示例创建具有两个区域的数据库帐户。 还可能创建单区域（指定为写入区域并且故障转移优先级值为 0）或多区域数据库帐户。 有关详细信息，请参阅[多区域数据库帐户][distribute-data-multiple-regionally]。
* 这些位置必须是已正式推出 Azure Cosmos DB 的区域。 [Azure 区域页面](https://www.azure.cn/support/service-dashboard/#services)提供当前的区域列表。

<a name="update-documentdb-account-powershell"></a>
##  <a name="update-an-azure-cosmos-db-database-account"></a>更新 Azure Cosmos DB 数据库帐户

此命令可更新 Azure Cosmos DB 数据库帐户属性。 这包括一致性策略和数据库帐户所在的位置。

> [!NOTE]
> 此命令可添加和删除区域，但不可修改故障转移优先级。 若要修改故障转移优先级，请参阅 [下文](#modify-failover-priority-powershell)。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties

* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值需要为 0。 每个数据库帐户必须有且只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值需要大于 0。 每个数据库帐户可以有多个读取区域。
* `<default-consistency-level>` Azure Cosmos DB 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `<ip-range-filter>` 指定 CIDR 格式的 IP 地址集或 IP 地址范围，将这些地址纳入给定数据库帐户所允许的客户端 IP 列表内。 IP 地址/范围必须以逗号分隔，且不得包含空格。 有关详细信息，请参阅 [Azure Cosmos DB 防火墙支持](firewall-support.md)
* `<max-interval>` 与有限过期一致性一起使用时，此值表示允许的过期时间（以秒为单位）。 此值的接受范围为 1-100。
* `<max-staleness-prefix>` 与有限过期一致性一起使用时，此值表示允许的过期请求数。 此值的接受范围为 1 - 2,147,483,647。
* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<resource-group-location>` 新 Azure Cosmos DB 数据库帐户所属的 Azure 资源组的位置。
* `<database-account-name>` 要更新的 Azure Cosmos DB 数据库帐户的名称。

示例： 

    $locations = @(@{"locationName"="China North"; "failoverPriority"=0}, @{"locationName"="China East"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

<a name="delete-documentdb-account-powershell"></a>
##  <a name="delete-an-azure-cosmos-db-database-account"></a>删除 Azure Cosmos DB 数据库帐户

此命令可删除现有 Azure Cosmos DB 数据库帐户。

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<database-account-name>` 要删除的 Azure Cosmos DB 数据库帐户的名称。

示例：

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

<a name="get-documentdb-properties-powershell"></a>
##  <a name="get-properties-of-an-azure-cosmos-db-database-account"></a>获取 Azure Cosmos DB 数据库帐户的属性

此命令可获取现有 Azure Cosmos DB 数据库帐户的属性。

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

<a name="update-tags-powershell"></a>
##  <a name="update-tags-of-an-azure-cosmos-db-database-account"></a>更新 Azure Cosmos DB 数据库帐户的标记

下面的示例介绍了如何设置 Azure Cosmos DB 数据库帐户的 [Azure 资源标记][azure-resource-tags]。

> [!NOTE]
> 通过此将 `-Tags` 标志追加到相应的参数，可将此命令与创建或更新命令组合。

示例：

    $tags = @{"dept" = "Finance"; environment = "Production"}
    Set-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts"  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

<a name="list-account-keys-powershell"></a>
##  <a name="list-account-keys"></a>列出帐户密钥

创建 Azure Cosmos DB 帐户时，该服务会生成两个主访问密钥，用于访问 Azure Cosmos DB 帐户时的身份验证。 Azure Cosmos DB 提供有两个访问密钥，因此可在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还提供用于对只读操作进行身份验证的只读密钥。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

<a name="list-connection-strings-powershell"></a>
##  <a name="list-connection-strings"></a>列出连接字符串

对于 MongoDB 帐户，可以使用以下命令检索将 MongoDB 应用连接到数据库帐户的连接字符串。

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

<a name="regenerate-account-key-powershell"></a>
##  <a name="regenerate-account-key"></a>重新生成帐户密钥

应定期更改 Azure Cosmos DB 帐户的访问密钥，加强连接的安全性。 将分配两个访问密钥，从而可以在使用一个访问密钥保持连接到 Azure Cosmos DB 帐户的同时，再生成另一个访问密钥。

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。
* `<key-kind>` 要重新生成的四种类型的密钥之一：["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"]。

示例：

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

<a name="modify-failover-priority-powershell"></a>
##  <a name="modify-failover-priority-of-an-azure-cosmos-db-database-account"></a>修改 Azure Cosmos DB 数据库帐户的故障转移优先级

对于多区域数据库帐户，可以更改 Azure Cosmos DB 数据库帐户所在的各个区域的故障转移优先级。 有关 Azure Cosmos DB 数据库帐户中的故障转移的详细信息，请参阅[使用 Azure Cosmos DB 在多个区域分配数据][distribute-data-multiple-regionally]。

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值需要为 0。 每个数据库帐户必须有且只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值需要大于 0。 每个数据库帐户可以有多个读取区域。
* `<resource-group-name>` 新的 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups] 的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $failoverPolicies = @(@{"locationName"="China East"; "failoverPriority"=0},@{"locationName"="China North"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>后续步骤

* 若要使用 .NET 进行连接，请参阅[使用 .NET 进行连接和查询](create-sql-api-dotnet.md)。
* 若要使用 Node.js 进行连接，请参阅[使用 Node.js 和 MongoDB 应用进行连接和查询](create-mongodb-nodejs.md)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell-install-configure
[scaling-multiple-regionally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-multiple-regionally]: distribute-data-globally.md
[azure-resource-groups]: /azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: /azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/

<!-- Update_Description: update meta properties, wording update -->