---
title: 使用 Azure 媒体分析检测面部和情绪 | Microsoft Docs
description: 本主题演示如何使用 Azure 媒体分析检测人脸和情感。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 02/10/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: d972ddbc792b894402d8875440bc2c86570d4ab6
ms.sourcegitcommit: 7b93bc945ba49490ea392476a8e9ba1a273098e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833393"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>使用 Azure 媒体分析检测面部和情绪
## <a name="overview"></a>概述
借助 **Azure Media Face Detector** 媒体处理器 (MP)，可通过面部表情来统计、跟踪动作，甚至计量受众的参与和反应。 此服务包含两项功能： 

* **面部检测**
  
    面部检测能够找出并跟踪视频中的人脸。 可以检测多个面部，随后随着对象移动进行跟踪，并将时间和位置的元数据以 JSON 文件的形式返回。 跟踪期间，该服务会在人员于屏幕上四处移动时，尝试为他们的面部赋予相同的 ID，即使他们被挡住或暂时离帧。
  
  > [!NOTE]
  > 此服务并不执行面部识别。 面部离帧或被挡住太久的人员，会在回来时赋予新的 ID。
  > 
  > 
* **情绪检测**
  
    情绪检测是面部检测媒体处理器的可选组件，它根据检测到的面部返回多个情绪属性的分析，包括快乐、悲伤、恐惧、愤怒等等。 

**Azure 媒体面部检测器** MP 目前以预览版提供。

本文提供了有关 **Azure Media Face Detector** 的详细信息，并演示了如何通过适用于 .NET 的媒体服务 SDK 使用它。

## <a name="face-detector-input-files"></a>面部检测器输入文件
视频文件。 目前支持以下格式：MP4、MOV 和 WMV。

## <a name="face-detector-output-files"></a>面部检测器输出文件
人脸检测器和跟踪 API 可提供高精确度的面部位置检测和跟踪功能，并在单个视频中检测到最多 64 个人脸。 正面的面部可提供最佳效果，而侧面的面部和较小的面部（小于或等于 24x24 像素）可能就无法获得相同的精确度。

已检测到并已跟踪的面部会在坐标（左侧、顶部、宽度和高度）中返回，其中会在以像素为单位的图像中指明面部的位置，以及表示正在跟踪该人员的面部 ID 编号。 在正面面部长时间于帧中消失或重叠的情况下，面部 ID 编号很容易重置，导致某些人员被分配多个 ID。

## <a id="output_elements"></a>输出 JSON 文件中的元素

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

面部检测器使用分片（元数据可以分解为基于时间的区块，可以只下载需要的部分）和分段（可以在事件数过于庞大的情况下对事件进行分解）技术。 一些简单的计算可帮助转换数据。 例如，如果事件从 6300（刻度）开始，其时间刻度为 2997（刻度/秒），帧速率为 29.97（帧/秒），那么：

* 开始时间/时间刻度 = 2.1 秒
* 秒数 x 帧速率 = 63 帧

## <a name="face-detection-input-and-output-example"></a>面部检测输入和输出示例
### <a name="input-video"></a>输入视频
[输入视频](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.chinacloudapi.cn%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>任务配置（预设）
在使用 **Azure 媒体面部检测器**创建任务时，必须指定配置预设。 以下配置预设仅适用于面部检测。

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>属性说明
| 属性名称 | 说明 |
| --- | --- |
| Mode |快速 - 处理速度快，但准确度较低（默认）。|

### <a name="json-output"></a>JSON 输出
下面是 JSON 输出被截断的示例。

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>情绪检测输入和输出示例
### <a name="input-video"></a>输入视频
[输入视频](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.chinacloudapi.cn%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>任务配置（预设）
在使用 **Azure 媒体面部检测器**创建任务时，必须指定配置预设。 以下配置预设指定基于情绪检测创建 JSON。

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>属性说明
| 属性名称 | 说明 |
| --- | --- |
| Mode |Faces：仅人脸检测。<br/>PerFaceEmotion：独立返回每个人脸检测的情绪。<br/>AggregateEmotion：返回帧中所有面部的平均情绪值。 |
| AggregateEmotionWindowMs |在已选择 AggregateEmotion 模式时使用。 指定用于生成每个聚合结果的视频的长度，以毫秒为单位。 |
| AggregateEmotionIntervalMs |在已选择 AggregateEmotion 模式时使用。 指定生成聚合结果的频率。 |

#### <a name="aggregate-defaults"></a>聚合默认值
下面是聚合窗口和间隔设置的建议值。 AggregateEmotionWindowMs 应该超过 AggregateEmotionIntervalMs。

|| 默认值 | 最大值 | 最小值 |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0.25|
| AggregateEmotionIntervalMs |0.5 |1 |0.25|

### <a name="json-output"></a>JSON 输出
聚合情绪的 JSON 输出（已截断）：

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>限制
* 支持的输入视频格式包括 MP4、MOV 和 WMV。
* 可检测的面部大小范围为 24x24 到 2048x2048 像素。 无法检测此范围以外的面部。
* 对于每个视频，返回的面部数上限为 64。
* 某些面部可能因技术难题而无法检测，例如非常大的面部角度（头部姿势），以及较大的阻挡物。 正面和接近正面的面部可提供最佳效果。

## <a name="net-sample-code"></a>.NET 示例代码

以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
2. 基于包含以下 json 预设的配置文件创建含有面部检测任务的作业： 

    ```json
            {
                "version": "1.0"
            }
    ```
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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Azure Media Analytics demos（Azure 媒体分析演示）](http://amslabs.azurewebsites.net/demos/Analytics.html)

<!--Update_Description: update Aggregate defaults table-->