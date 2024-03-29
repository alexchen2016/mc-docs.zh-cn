---
title: 在 Azure Stack 中将 API 版本配置文件与 .NET SDK 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 .NET 配合使用。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 12/07/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 7e39c95d06750f579c971e10c9ab9ad8c469cf9c
ms.sourcegitcommit: 20bff6864fd10596b5fc2ac8e059629999da8ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135425"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 .NET 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

用于 Azure Stack 资源管理器的 .NET SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了计算、网络、存储、应用服务和 [KeyVault](/key-vault/key-vault-whatis)。 .NET SDK 包括 14 NuGet 包。 这些内含配置文件信息的包每次都必须下载到项目解决方案。 不过，可以专门下载用于 2018-03-01-hybrid 或 2017-03-09-profile 的资源提供程序，以便优化应用程序的内存。 每个包都包含资源提供程序、相应的 API 版本以及所属 API 配置文件。 .NET SDK 中的 API 配置文件可以用来在公有云 Azure 资源和 Azure Stack 上的资源之间进行切换，实现混合云开发。

## <a name="net-and-api-version-profiles"></a>.NET 与 API 版本配置文件

API 配置文件是资源提供程序和 API 版本的组合。 可以使用 API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。

-   若要使用所有服务的最新版本，请使用包的 **latest** 配置文件。 该配置文件是 **Microsoft.Azure.Management** NuGet 包的一部分。

-   若要使用与 Azure Stack 兼容的服务，请使用 **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** 或 **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg** 包。

    -   每个配置文件的每个资源提供程序都有两个包。

    -   确保将上述 NuGet 包的 **ResourceProvider** 部分更改为正确的提供程序。

-   若要使用某个服务的最新 API-version，请使用特定 NuGet 包的 **Latest** 配置文件。 例如，若要单独使用计算服务的 **latest-API** 版本，请使用 **compute** 包的 **latest** 配置文件。 **latest** 配置文件是 **Microsoft.Azure.Management** NuGet 包的一部分。

-   若要使用特定资源提供程序中某个资源类型的特定 API-version，请使用在包中定义的特定 API 版本。

可以在同一应用程序中组合所有选项。

## <a name="install-the-azure-net-sdk"></a>安装 Azure .NET SDK

1.  安装 Git。 有关说明，请参阅[入门 - 安装 Git][]。

2.  若要安装正确的 NuGet 包，请参阅[查找和安装包][]。

3.  需安装的包取决于要使用的配置文件版本。 配置文件版本的包名如下：

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  若要安装 Visual Studio Code 的正确 NuGet 包，请查看此下载链接：[NuGet 包管理器说明][]。

5.  如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的说明，请参阅[在 Azure Stack 中创建产品/服务的订阅][]。

6.  创建服务主体并保存客户端 ID 和客户端机密。 有关如何为 Azure Stack 创建服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限][]。 创建服务主体时的客户端 ID 也称为应用程序 ID。

7.  确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限][]。

## <a name="prerequisites"></a>先决条件

若要将 .NET Azure SDK 与 Azure Stack 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅表后针对操作系统的说明。

| Value                     | 环境变量   | 说明                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID                 | AZURE_TENANT_ID       | Azure Stack [租户 ID  ][] 的值。                                                                          |
| 客户端 ID                 | AZURE_CLIENT_ID       | 在本文上一部分创建服务主体时保存的服务主体应用程序 ID。 |
| 订阅 ID           | AZURE_SUBSCRIPTION_ID | [订阅 ID  ][] 用于访问 Azure Stack 中的产品/服务。                                                      |
| 客户端机密             | AZURE_CLIENT_SECRET   | 创建服务主体时保存的服务主体应用程序机密。                                      |
| 资源管理器终结点 | ARM_ENDPOINT           | 请参阅 [Azure Stack 资源管理器终结点  ][]。                                                                    |
| 位置                  | RESOURCE_LOCATION     | Azure Stack 的位置。

若要查找 Azure Stack 的租户 ID，请按[此处](../operator/azure-stack-csp-ref-operations.md)提供的说明操作。 若要设置环境变量，请执行以下步骤：

### <a name="microsoft-windows"></a>Microsoft Windows

若要在 Windows 命令提示符中设置环境变量，请使用以下格式：

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>基于 MacOS、Linux 和 Unix 的系统

在基于 Unix 的系统中，可以使用以下命令：

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack 资源管理器终结点

Azure 资源管理器是一种管理框架，可供管理员用来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行代码所需的信息。

请注意以下事项：

- Azure Stack 开发工具包 (ASDK) 中的 **ResourceManagerUrl** 为： https://management.local.azurestack.external/

- 集成系统中的 **ResourceManagerUrl** 为：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` 检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

示例 JSON 文件：

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.chinacloudapi.cn/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.chinacloudapi.cn/",
      "audiences": ["https://management.yourtenant.partner.onmschina.cn/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>现有 API 配置文件

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**：为 Azure Stack 生成的最新配置文件。 请将此配置文件用于与 Azure Stack 最兼容的服务，前提是使用 1808 或更高的戳记。

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**：如果使用的戳记低于版本 1808，请使用此配置文件。

3.  **最新**：包含所有服务的最新版本的配置文件。 使用所有服务的最新版本。 该配置文件是 **Microsoft.Azure.Management** NuGet 包的一部分。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要][]。

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API 配置文件的使用

应该使用以下代码来实例化资源管理客户端。 可以使用类似的代码来实例化其他资源提供程序（例如计算、网络和存储）客户端。 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

以上代码中的 `credentials` 参数是实例化客户端所需的。 以下代码根据租户 ID 和服务主体生成一个身份验证令牌。

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
代码中的 `getActiveDirectoryServiceSettings` 调用从元数据终结点检索 Azure Stack 终结点。 它从所做的调用声明环境变量： 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
这样即可使用 API 配置文件 NuGet 包将应用程序成功部署到 Azure Stack。

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

若要使用 .NET 和 Azure Stack API 配置文件来创建解决方案，可以将以下示例用作参考。
- [管理资源组](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [管理存储帐户](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [管理虚拟机](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md)
- [配置文件支持的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)

