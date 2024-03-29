---
title: 示例：实时视频分析 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 使用人脸 API 对从实时视频流中获取的帧执行近实时分析。
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
origin.date: 03/01/2018
ms.date: 01/17/2019
ms.author: v-junlch
ms.openlocfilehash: 505f87838d7edcb9e14f2a3b0d01e45e01941cda
ms.sourcegitcommit: df1adc5cce721db439c1a7af67f1b19280004b2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63852156"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>示例：如何实时分析视频

本指南将演示如何对实时视频流中提取的帧执行近实时分析。 此类系统中的基本组件包括：

- 从视频源中获取帧
- 选择要分析的帧
- 将这些帧提交到 API
- 使用从 API 调用返回的每个分析结果

这些示例是使用 C# 编写的，代码可以在 GitHub 上找到，网址为：[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)。

## <a name="the-approach"></a>方法

有多种方法可以解决对视频流运行近实时分析的问题。 首先，我们从易到难概述三种方法。

### <a name="a-simple-approach"></a>简单方法

近实时分析系统的最简单设计是无限循环，在每次迭代中捕捉一个帧，对它进行分析，然后使用结果：

```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

如果分析包括轻量客户端算法，则适合使用此方法。 但是，当在云端进行分析时，所涉及的延迟意味着 API 调用可能需要几秒钟的时间。 在此期间，我们不会捕获图像，而线程基本上不执行任何操作。 最大帧速率受到 API 调用延迟的限制。

### <a name="parallelizing-api-calls"></a>并行化 API 调用

尽管简单的单线程循环适合轻量客户端算法，但却无法很好地适应云 API 调用存在的延迟。 此问题的解决方法是让长时间运行的 API 调用与抓帧操作并行执行。 在 C# 中，可以使用基于任务的并行度来实现此目的，例如：

```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

此代码将在单独的任务中启动每个分析，当我们继续捕捉新帧时，这些任务可以在后台运行。 使用此方法时，我们可以避免在等待 API 调用返回时阻塞主线程，但是失去了简单版本提供的一些保证。 多个 API 调用可能并行执行，但结果可能以错误的顺序返回。 这也可能导致多个线程同时进入 ConsumeResult() 函数，如果该函数非线程安全，这可能会很危险。 最后，此简单代码不会跟踪所创建的任务，异常会以无提示方式消失。 因此，最终步骤是添加“使用者”线程，它将跟踪分析任务，引发异常，终止长时间运行的任务，并确保以正确的顺序使用结果。

### <a name="a-producer-consumer-design"></a>生成者-使用者设计

在最终的“生产者-使用者”系统中，我们有一个生产者线程，看起来与我们之前的无限循环类似。 但是，生成者不会在分析结果可用后立即使用这些结果，而仅仅是将任务放入队列，以对其进行跟踪。

```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

我们还有一个使用者线程，它会将任务从队列中取出，等待它们完成，并显示结果或引发已引发的异常。 使用队列可以保证按正确的顺序逐个使用结果，而不会限制系统的最大帧速率。

```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>实现解决方案

### <a name="getting-started"></a>入门

为了尽快启动和运行应用，需灵活实施上述系统。 若要访问代码，请转到 [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)。

该库包含 FrameGrabber 类，该类会通过实现上述生产者-使用者系统来处理网络摄像头中的视频帧。 用户可以指定 API 调用的确切形式，该类将使用事件来让调用代码知道何时获取新帧或者新的分析结果何时可用。

为了说明一些可能性，下面例举了使用该库的两个示例应用。 第一个是简单的控制台应用，下面再现了它的简化版本。 此应用从默认网络摄像头抓帧，并将其提交给人脸 API 进行人脸检测。

```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<Subscription Key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

第二个示例应用更有趣，允许选择对视频帧调用哪个 API。 在左侧，应用显示实时视频预览，在右侧，它显示重叠在相应帧上的最新 API 结果。

在大多数模式下，左侧的实时视频与右侧的可视化分析之间存在明显的延迟。 这种延迟是发出 API 调用所花费的时间。 例外情况是“EmotionsWithClientFaceDetect”模式，它使用 OpenCV 在客户端计算机上本地执行人脸检测，然后将全部图像提交给认知服务。 这样我们就可以立即可视化检测到的人脸，然后在 API 调用返回后更新情感。 这是“混合”方法的示例，其中的客户端可以执行一些简单的处理，认知服务 API 可以在需要时通过更高级的分析对其进行补充。

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>集成到代码库中

若要开始使用此示例，请按照下列步骤操作：

1. 从[订阅](https://www.azure.cn/pricing/1rmb-trial/)获取视觉 API 的 API 密钥。 对于视频帧分析，适用的 API 包括：
    - [计算机视觉 API](/cognitive-services/computer-vision/home)
    - [情感 API](/cognitive-services/emotion/home)
    - [人脸 API](/cognitive-services/face/overview)

2. 克隆 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 存储库

3. 在 Visual Studio 2015 中打开示例，然后生成并运行示例应用程序：
    - 对于 BasicConsoleSample，人脸 API 密钥已在  [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 中直接进行硬编码。
    - 对于 LiveCameraSample，应在应用的“设置”窗格中输入密钥。 在切换不同的会话后，这些密钥将持久保存为用户数据。
        

当准备好进行集成时，**请从你自己的项目中引用 VideoFrameAnalyzer 库。** 

## <a name="summary"></a>摘要

本指南介绍了如何使用人脸 API、计算机视觉 API 和情感 API 对实时视频流运行近实时分析，以及如何使用我们的示例代码开始操作。  

请随时在 [GitHub 存储库](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)中提供反馈和建议，或者在我们的  [UserVoice 站点](https://cognitive.uservoice.com/)上提供更广泛的 API 反馈。



## <a name="related"></a> 相关主题
- [如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)
- [如何检测图像中的人脸](HowtoDetectFacesinImage.md)

<!-- Update_Description: wording update -->