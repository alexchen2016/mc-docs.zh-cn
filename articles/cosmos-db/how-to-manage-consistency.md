---
title: 了解如何在 Azure Cosmos DB 中管理一致性
description: 了解如何在 Azure Cosmos DB 中管理一致性
author: rockboyfor
ms.service: cosmos-db
ms.topic: sample
origin.date: 05/23/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 8b9517a394026ee7f773e82d912e100f8ac772e0
ms.sourcegitcommit: 43eb6282d454a14a9eca1dfed11ed34adb963bd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151402"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中管理一致性级别

本文介绍了如何在 Azure Cosmos DB 中管理一致性级别。 你将了解如何配置默认一致性级别、替代默认一致性、手动管理会话令牌以及了解概率有限过期 (PBS) 指标。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

[默认一致性级别](consistency-levels.md)是客户端默认情况下使用的一致性级别。 客户端总是可以替代它。

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

此示例在“中国东部”和“中国北部”区域中创建启用了多个写入区域的新 Azure Cosmos 帐户。 默认一致性级别设置为“会话”一致性  。

```powershell
$locations = @(@{"locationName"="China East"; "failoverPriority"=0},
             @{"locationName"="China North"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "China East" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure 门户

若要查看或修改默认一致性级别，请登录到 Azure 门户。 找到你的 Azure Cosmos 帐户，打开“默认一致性”窗格  。 选择你希望用作新的默认值的一致性级别，然后选择“保存”  。

<!--Not Availabel on 06/14/2019 The Azure portal also provides a visualization of different consistency levels with music notes. -->

![Azure 门户中的一致性菜单](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>替代默认一致性级别

客户端可以重写由服务设置的默认一致性级别。 可以在每个请求上设置一致性级别，这将替代在帐户级别设置的默认一致性级别。

<a name="override-default-consistency-dotnet"></a>
### <a name="net-sdk"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

<a name="override-default-consistency-java-async"></a>
### <a name="java-async-sdk"></a>Java 异步 SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

<a name="override-default-consistency-java-sync"></a>
### <a name="java-sync-sdk"></a>Java 同步 SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

<a name="override-default-consistency-javascript"></a>
### <a name="nodejsjavascripttypescript-sdk"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

<a name="override-default-consistency-python"></a>
### <a name="python-sdk"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>利用会话令牌

Azure Cosmos DB 中的一致性级别之一是“会话”一致性  。 这是默认情况下应用于 Cosmos 帐户的默认级别。 使用“会话”一致性时，客户端将在内部的每个读取/查询请求中使用会话令牌，以确保维持设置的一致性级别  。

若要手动管理会话令牌，请从响应中获取会话令牌并针对每个请求设置它们。 如果不需手动管理会话令牌，则不需要使用这些示例。 SDK 会自动跟踪会话令牌。 如果未手动设置会话令牌，则默认情况下，SDK 使用最新的会话令牌。

<a name="utilize-session-tokens-dotnet"></a>
### <a name="net-sdk"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

<a name="utilize-session-tokens-java-async"></a>
### <a name="java-async-sdk"></a>Java 异步 SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

<a name="utilize-session-tokens-java-sync"></a>
### <a name="java-sync-sdk"></a>Java 同步 SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

<a name="utilize-session-tokens-javascript"></a>
### <a name="nodejsjavascripttypescript-sdk"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

<a name="utilize-session-tokens-python"></a>
### <a name="python-sdk"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>监视概率有限过期 (PBS) 指标

最终一致性的最终程度如何？ 对于普通情况，我们是否可以提供版本历史和时间方面的过期限度。 [**概率有限过期 (PBS)** ](https://pbs.cs.berkeley.edu/) 指标尝试量化过期的概率并将其显示为指标。 若要查看 PBS 指标，请在 Azure 门户中转到你的 Cosmos 帐户。 打开“指标”  窗格，然后选择“一致性”  选项卡。查看名为“基于工作负载的强一致性读取的概率(请参阅 PBS)”的图。 

![Azure 门户中的 PBS 图](./media/how-to-manage-consistency/pbs-metric.png)

## <a name="next-steps"></a>后续步骤

详细了解如何管理数据冲突，或者继续了解 Azure Cosmos DB 中的下一个关键概念。 请参阅以下文章：

* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [管理区域之间的冲突](how-to-manage-conflicts.md)
* [分区和数据分布](partition-data.md)
* [现代分布式数据库系统设计中的一致性利弊](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [高可用性](high-availability.md)
* [Azure Cosmos DB SLA](https://www.azure.cn/support/sla/cosmos-db/)

<!-- Update_Description: update meta properties, wording update -->