---
title: 使用 Azure Media Encoder Standard 自动生成比特率阶梯 | Microsoft 文档
description: 本主题介绍如何使用 Media Encoder Standard (MES) 根据输入分辨率和比特率自动生成比特率阶梯。 不会超过输入分辨率和比特率。 例如，如果输入在 3Mbps 时为 720p，则输出最多会保持 720p，并且会以低于 3Mbps 的速率开始。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/14/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: a687330596d16a56f9baf7d080a1bef5729b27c8
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624152"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>使用 Azure Media Encoder Standard 自动生成比特率阶梯  

## <a name="overview"></a>概述

本文介绍如何使用 Media Encoder Standard (MES) 根据输入分辨率和比特率自动生成比特率阶梯（比特率-分辨率对）。 自动生成的预设不会超过输入分辨率和比特率。 例如，如果输入在 3 Mbps 时为 720p，则输出最多会保持 720p，并且会以低于 3 Mbps 的速率开始。

### <a name="encoding-for-streaming-only"></a>编码为仅流式处理

如果打算将源视频编码为仅流式处理，则应在创建编码任务时使用“自适应流式处理”预设。 使用“自适应流式处理”预设时，MES 编码器会以智能方式为比特率阶梯设置一个上限。 但是，用户无法控制编码成本，因为是由服务确定要使用的层数和分辨率。 可以在本文末尾看到 MES 生成的输出层示例，这些示例是使用“自适应流式处理”预设进行编码得来的。 该输出资产包含无音频和视频交错的 MP4 文件。

### <a name="encoding-for-streaming-and-progressive-download"></a>编码为流式处理和渐进式下载

如果打算将源视频编码为流式处理以及生成可供渐进式下载的 MP4 文件，则应在创建编码任务时使用“内容自适应多比特率 MP4”预设。 使用“内容自适应多比特率 MP4”预设时，MES 编码器应用与上述相同的编码逻辑，但现在输出资产会包含音频和视频交错的 MP4 文件。 可使用其中一个 MP4 文件（例如，最高比特率版本）作为渐进式下载文件。

## <a id="encoding_with_dotnet"></a>使用媒体服务 .NET SDK 进行编码

以下代码示例使用媒体服务 .NET SDK 执行下列任务：

- 创建编码作业。
- 获取对 Media Encoder Standard 编码器的引用。
- 向作业添加一个编码任务，指定使用“自适应流式处理”预设。 
- 创建包含所编码资产的输出资产。
- 添加事件处理程序以检查作业进度。
- 提交作业。

#### <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并在 app.config 文件中填充连接信息，如[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述。 

#### <a name="example"></a>示例

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureChinaCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a id="output"></a>输出

此部分显示 MES 生成的输出层的三个示例，是使用“自适应流式处理”预设进行编码得来的。 

### <a name="example-1"></a>示例 1
高度为“1080”，帧速率为“29.970”的源生成 6 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>示例 2
高度为“720”，帧速率为“23.970”的源生成 5 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>示例 3
高度为“360”，帧速率为“29.970”的源生成 3 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="see-also"></a>另请参阅
[媒体服务编码概述](media-services-encode-asset.md)
<!--Update_Description: update code to use AAD token instead of ACS-->
