---
title: 迁移到群集配置的基于角色的细化访问权限 - Azure HDInsight
description: 了解如何做出所需的更改，以迁移到群集配置的基于角色的细化访问权限。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: a1fb54eb029cc44888776dd9c7e683ce443ef7ee
ms.sourcegitcommit: 99ef971eb118e3c86a6c5299c7b4020e215409b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65829267"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>迁移到群集配置的基于角色的细化访问权限

我们正在引入一些重要更改，以支持使用更细化的基于角色的访问来获取敏感信息。 在这些更改中，如果你正在使用某个[受影响的实体/方案](#am-i-affected-by-these-changes)，则**可能需要采取某种措施**。

## <a name="what-is-changing"></a>有什么变化？

以前，处理“所有者”、“参与者”或“读取者”[RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)的群集用户可以通过 HDInsight API 获取机密。
今后，这些机密不再可供具有“读取者”角色的用户访问。 机密定义为值，可用于获取比用户角色允许的访问权限更高的权限。 这些值包括群集网关 HTTP 凭据、存储帐户密钥和数据库凭据等值。

他们可以检索机密，而无需向其授予“参与者”或“所有者”管理权限。 总结：

| 角色                                  | 以前                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 读取器                                | - 读取访问权限，包括机密                                                                   | - 读取访问权限，**不**包括机密 |           |   |   |
| HDInsight 群集操作员<br>（新角色） | 不适用                                                                                              | - 读/写访问权限，包括机密         |   |   |
| 参与者                           | - 读/写访问权限，包括机密<br>- 创建和管理所有类型的 Azure 资源。     | 无变化 |
| 所有者                                 | - 读/写访问权限，包括机密<br>- 对所有资源的完全访问权限<br>- 将访问权限委托给其他人 | 无变化 |

了解如何向用户添加 HDInsight 群集操作员角色分配，以授予其对群集机密的读/写访问权限的信息，请参阅以下部分[将 HDInsight 群集操作员角色分配添加到用户](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我是否受此更改的影响？

以下实体和方案将受到影响：

- [API](#api)：使用 `/configurations` 或 `/configurations/{configurationName}` 终结点的用户。
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 或更低版本。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0 或更低版本。
- [SDK for .NET](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x)：从 ConfigurationsOperationsExtensions 类使用 `GetClusterConfigurations`、`GetConnectivitySettings`、`ConfigureHttpSettings`、`EnableHttp` 或 `DisableHttp` 方法的用户。
    - [版本 3.x 和以上](#versions-3x-and-up)：从 `ConfigurationsOperationsExtensions` 类使用 `Get`、`Update`、`EnableHttp` 或 `DisableHttp` 方法的用户。
- [SDK for Python](#sdk-for-python)：从 `ConfigurationsOperations` 类使用 `get` 或 `update` 方法的用户。
- [SDK for Java](#sdk-for-java)：从 `ConfigurationsInner` 类使用 `update` 或 `get` 方法的用户。
- [SDK for Go](#sdk-for-go)：从 `ConfigurationsClient` 构造使用 `Get` 或 `Update` 方法的用户。

请参阅以下部分（或使用上面的链接）了解适用于你的方案的迁移步骤。

### <a name="api"></a>API

以下 API 将会更改或弃用：

- [**GET /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration)（已删除敏感信息）
    - 以前用于获取单个配置类型（包括机密）。
    - 此 API 调用现在会返回省略机密的单个配置类型。 若要获取所有配置（包括机密），请使用新的 POST /configurations 调用。 如果只要获取网关设置，请使用新的 POST /getGatewaySettings 调用。
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations)（已弃用）
    - 以前用于获取所有配置（包括机密）
    - 将不再支持此 API 调用。 今后若要获取所有配置，请使用新的 POST /configurations 调用。 若要获取省略敏感参数的配置，请使用 GET /configurations/{configurationName} 调用。
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings)（已弃用）
    - 以前用于更新网关凭据。
    - 此 API 调用将被弃用且不再受支持。 请改用新的 POST /updateGatewaySettings。

已添加以下替换用的 API：</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 可以获取所有配置（包括机密）。
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 可以获取网关设置。
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 可以更新网关设置（用户名和/或密码）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

如果使用版本 1.1.1 或更低版本，请更新到[最新版本的 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)，以避免中断。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果使用版本 3.20.0 或更低版本，请更新到[最新版本的 Azure Toolkit for IntelliJ 插件](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)，以避免中断。

### <a name="sdk-for-net"></a>用于 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

请更新到 HDInsight SDK for .NET [版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0)。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 将**不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 `ClusterOperationsExtensions.ListConfigurations`。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。
    - 如果只要检索 HTTP 网关凭据，请使用 `ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.GetConnectivitySettings` 现已弃用，已由 `ClusterOperationsExtensions.GetGatewaySettings` 取代。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 现已弃用，已由 `ClusterOperationsExtensions.UpdateGatewaySettings` 取代。

- `ConfigurationsOperationsExtensions.EnableHttp` 和 `DisableHttp` 现已弃用。 现在始终会启用 HTTP，因此不再需要这些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 和以上

请更新到 HDInsight SDK for .NET [版本 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 将**不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 现已弃用，已由 [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) 取代。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 和 [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) 现已弃用。 现在始终会启用 HTTP，因此不再需要这些方法。

### <a name="sdk-for-python"></a>用于 Python 的 SDK

请更新到 HDInsight SDK for Python [版本 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 将**不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ConfigurationsOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) 现已弃用，已由 [`ClusterOperationsExtensions.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 取代。

### <a name="sdk-for-java"></a>SDK For Java

请更新到 HDInsight SDK for Java [版本 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) 将**不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ConfigurationsOperations.get_gateway_settings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) 现已弃用，已由 [`ClusterOperationsExtensions.update_gateway_settings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable) 取代。

### <a name="sdk-for-go"></a>SDK For Go

请更新到 HDInsight SDK for Go [版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) 将**不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) 现已弃用，已由 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) 取代。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>将 HDInsight 群集操作员角色分配添加到用户

具有[参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)或[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色的用户可以向你希望他们对敏感的 HDInsight 群集配置值（例如群集网关凭据和存储帐户密钥）拥有读/写访问权限的用户分配 [HDInisght 群集操作员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

添加此角色分配的最简单方法是在 Azure CLI 中使用 `az role assignemnt create` 命令。

> [!NOTE]
> 此命令必须由具有“参与者”或“所有者”角色的用户运行，因为只有他们才能授予这些权限。 `--assignee` 是要将 HDInsight 群集操作员角色分配到的用户的电子邮件地址。

#### <a name="grant-role-at-the-resource-cluster-level"></a>在资源（群集）级别授予角色

```azurecli
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>在资源组级别授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>在订阅级别授予角色

```azurecli
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 门户

或者，可以使用 Azure 门户将 HDInsight 群集操作员角色分配添加到用户。 请参阅文档[使用 RBAC 和 Azure 门户管理对 Azure 资源的访问 - 添加角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。
