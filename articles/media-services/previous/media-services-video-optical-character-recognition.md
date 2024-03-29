---
title: 使用 Azure 媒体分析 OCR 将文本数字化 | Microsoft 文档
description: Azure 媒体分析 OCR（光学字符识别）可让你将视频文件中的文本内容转换成可编辑、可搜索的数字文本。  这可让你从媒体的视频信号中自动提取有意义的元数据。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 03/20/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: fcae37c93d0d3c29c98bfdd87c9619a024f185ec
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624207"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>使用 Azure 媒体分析将视频文件中的文本内容转换为数字文本  
## <a name="overview"></a>概述
如果需要提取视频文件的文本内容，并生成可编辑、可搜索的数字文本，则应该使用 Azure 媒体分析 OCR（光学字符识别）。 此 Azure 媒体处理器可检测视频文件的文本内容并生成文本文件供你使用。 OCR 可让你从媒体的视频信号中自动提取有意义的元数据。

与搜索引擎配合使用时，可以根据文本轻松编制媒体的索引，并增强发现内容的能力。 这在包含大量文本的视频（例如视频录制或者幻灯片演示屏幕截图）中非常有用。 Azure OCR 媒体处理器已针对数字文本进行了优化。

**Azure 媒体 OCR** 媒体处理器目前以预览版提供。

本文提供了有关 **Azure 媒体 OCR** 的详细信息，并演示了如何通过适用于 .NET 的媒体服务 SDK 使用它。 有关详细信息和示例，请参阅[此博客](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)。

## <a name="ocr-input-files"></a>OCR 输入文件
视频文件。 目前支持以下格式：MP4、MOV 和 WMV。

## <a name="task-configuration"></a>任务配置
任务配置（预设） 在使用 **Azure 媒体 OCR** 创建任务时，必须使用 JSON 或 XML 指定配置预设。 

>[!NOTE]
>在高度/宽度上，OCR 引擎仅将最小 40 像素到最大 32000 像素的图像区域视为有效输入。
>

### <a name="attribute-descriptions"></a>属性说明
| 属性名称 | 说明 |
| --- | --- |
|AdvancedOutput| 如果将 AdvancedOutput 设置为 true，则 JSON 输出将包含每个单词的位置数据（除了短语和区域以外）。 如果不想查看这些详细信息，请将标志设置为 false。 默认值为 false。 有关详细信息，请参阅[此博客](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/)。|
| 语言 |（可选）描述要查找的文本的语言。 下列类型作之一：AutoDetect（默认值）、Arabic、简体中文、繁体中文、Czech Danish、Dutch、English、Finnish、French、German、Greek、Hungarian、Italian、Japanese、Korean、Norwegian、Polish、Portuguese、Romanian、Russian、SerbianCyrillic、SerbianLatin、Slovak、Spanish、Swedish、Turkish。 |
| TextOrientation |（可选）描述要查找的文本的方向。  “Left”表示所有字母顶部朝向左侧。  默认文本（例如书籍中出现的文本）的方向为“Up”。  下列类型作之一：AutoDetect（默认值）、Up、Right、Down、Left。 |
| TimeInterval |（可选）描述采样率。  默认值为每 1/2 秒。<br/>JSON 格式 - HH:mm:ss.SSS（默认值 00:00:00.500）<br/>XML 格式 - W3C XSD 持续时间基元（默认值 PT0.5） |
| DetectRegions |（可选）指定要在其中检测文本的视频帧中的区域的 DetectRegion 对象数组。<br/>DetectRegion 对象由以下四个整数值组成：<br/>Left – 左边距中的像素<br/>Top – 上边距中的像素<br/>Width – 以像素为单位的区域宽度<br/>Height – 以像素为单位的区域高度 |

#### <a name="json-preset-example"></a>JSON 预设示例

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>XML 预设示例

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR 输出文件
OCR 媒体处理器的输出是一个 JSON 文件。

### <a name="elements-of-the-output-json-file"></a>输出 JSON 文件中的元素
视频 OCR 输出针对视频中的字符提供时间分段数据。  可以使用属性（例如语言或方向）来锁定你想要分析的文本。 

输出包含以下属性：

| 元素 | 说明 |
| --- | --- |
| 时间刻度 |视频每秒的“刻度”数 |
| Offset |时间戳的时间偏移量。 在版本 1.0 的视频 API 中，此属性始终为 0。 |
| Framerate |视频的每秒帧数 |
| width |以像素为单位的视频宽度 |
| height |以像素为单位的视频高度 |
| Fragments |将元数据堆积成的基于时间的视频块数组 |
| start |片段的开始时间（以“刻度”为单位） |
| duration |片段的长度（以“刻度”为单位） |
| interval |给定片段中每个事件的间隔 |
| events |包含区域的数组 |
| region |表示检测到的单词或短语的对象 |
| 语言 |区域中检测到的文本的语言 |
| orientation |区域中检测到的文本的方向 |
| lines |区域中检测到的文本的行数组 |
| text |实际文本 |

### <a name="json-output-example"></a>JSON 输出示例
以下输出示例包含常规视频信息和多个视频片段。 每个视频片段包含 OCR MP 检测到的每个区域及其语言和文本方向。 区域还包含此区域中的每个单词行，以及该行的文本、位置及其中每个单词的信息（单词内容、位置和置信度）。 下面是一个示例，我在其中嵌入了一些注释。

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET 示例代码

以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
2. 使用 OCR 配置/预设文件创建作业。
3. 下载输出 JSON 文件。 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并在 app.config 文件中填充连接信息，如[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述。 

#### <a name="example"></a>示例

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }

    }
}
```

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](media-services-analytics-overview.md)
<!--Update_Description: update code to use AAD token instead of ACS-->
