---
title: 快速入门：适用于 .NET 的内容审查器 SDK 帮助程序方法
titlesuffix: Azure Cognitive Services
description: 如何使用适用于 .NET 的 Azure 内容审查器 SDK 返回内容审查器客户端
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
origin.date: 01/04/2018
ms.date: 01/22/2019
ms.author: v-junlch
ms.openlocfilehash: 466665f4e214c852bf7fb02a9d6a0efd1b3d5d93
ms.sourcegitcommit: f248afb1039011d34579baed2980f0632061f5b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54858075"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>快速入门：帮助程序代码返回内容审查器客户端

本文提供了信息和代码示例，帮助你开始使用适用于 .NET 的内容审查器 SDK 来为订阅创建内容审查器。

本节中的其他快速入门也使用该库。

本文假定你已熟悉 Visual Studio 和 C#。

> [!IMPORTANT]
> 此类库包含仅用于演示目的的代码。
> 如果你将此代码用于生产，请使用存储和使用内容审查器订阅密钥的安全方法。

## <a name="sign-up-for-content-moderator-services"></a>注册内容审查器服务

必须有订阅密钥，才能通过 REST API 或 SDK 使用内容审查器服务。 在 [Azure 门户](https://portal.azure.cn/#create/Microsoft.CognitiveServicesContentModerator)中订阅内容审查器服务以获取它。

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 新建类库 (.NET Framework) 项目。

   在同一代码中，我将该项目命名为 ModeratorHelper

1. 添加对 System.Configuration 框架集的引用。

### <a name="install-required-packages"></a>安装所需程序包

安装以下 NuGet 包：

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Create the Content Moderator client

使用以下代码替换 ModeratorHelper.cs 文件的内容：

```c#
    using Microsoft.Azure.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, chinaeast2.
        /// </summary>
        private static readonly string AzureRegion = "<your region>";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.azure.cn";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
        }
    }
```

> [!IMPORTANT]
> 使用区域标识符和订阅密钥的值更新 AzureRegion 和 CMSubscriptionKey 字段。

你现在可以快速为订阅创建内容审查器客户端。

## <a name="next-steps"></a>后续步骤

下载本教程以及其他 .NET 内容审查器快速入门的 [Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，并开始集成。
