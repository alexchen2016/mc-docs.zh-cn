---
title: 将媒体服务资产下载到计算机 - Azure | Microsoft 文档
description: 了解如何将资产下载到计算机。 代码示例用 C# 编写且使用适用于 .NET 的媒体服务 SDK。
services: media-services
documentationcenter: ''
author: hayley244
manager: digimobile
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 08/09/2017
ms.date: 09/04/2017
ms.author: v-haiqya
ms.openlocfilehash: 7641e1fc086262ff9824d5b332b9386c451856fe
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52644733"
---
# <a name="how-to-deliver-an-asset-by-download"></a>如何：通过下载交付资产
本文介绍已上传到媒体服务的媒体资产的交付选项。 可以采用众多的应用程序方案来交付媒体服务内容。 编码后，下载生成的媒体资产，或使用流式处理定位符访问这些资产。 为了提高性能和伸缩性，用户还可以使用内容分发网络 (CDN) 来传送内容。

此示例演示如何将媒体资产从媒体服务下载到本地计算机。 该代码将按作业 ID 查询与媒体服务帐户关联的作业，并访问其 OutputMediaAssets 集合（即运行作业后生成的、包含一个或多个输出媒体资产的集）。 此示例演示如何通过作业下载输出媒体资产，但可以运用相同的方法来下载其他资产。

>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，请使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 有关详细信息，请参阅[本文](media-services-dotnet-manage-entities.md#limit-access-policies)。

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="see-also"></a>另请参阅
[交付流内容](media-services-deliver-streaming-content.md)

