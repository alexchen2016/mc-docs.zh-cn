---
title: 使用 .NET SDK 管理流式处理终结点。 | Microsoft Docs
description: 本文说明如何使用 Azure 门户管理流式处理终结点。
services: media-services
documentationcenter: ''
author: WenJason
writer: juliako
manager: digimobile
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: b9b9abddd731d188be993c4b81455127e5f3c96c
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624190"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>使用 .NET SDK 管理流式处理终结点  

>[!NOTE]
>请确保查看[概述](media-services-streaming-endpoints-overview.md)一文。 另请查看 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)。

本文中的代码演示了如何使用 Azure 媒体服务 .NET SDK 执行以下任务：

- 检查默认的流式处理终结点。
- 创建/添加新的流式处理终结点。

    如果计划使用不同的 CDN 或者使用一个 CDN 和直接访问，则可能需要多个流式处理终结点。

    > [!NOTE]
    > 仅当流式处理终结点处于运行状态时才进行计费。
    
- 更新流式处理终结点。
    
    请确保调用 Update() 函数。

- 删除流式处理终结点。

    >[!NOTE]
    >无法删除默认流式处理终结点。

有关如何缩放流式处理终结点的信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)文章。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并在 app.config 文件中填充连接信息，如[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述。 

## <a name="add-code-that-manages-streaming-endpoints"></a>添加管理流式处理终结点的代码
    
将 Program.cs 中的代码替换为以下代码：

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
    class Program
    {
        // Read values from the App.config file.

        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureChinaCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

