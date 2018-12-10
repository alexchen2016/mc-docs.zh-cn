---
title: 教程：识别图像中人脸的情感 - 情感 API、C#
titlesuffix: Azure Cognitive Services
description: 探索用于识别图像中人脸所表达的情感的基本 Windows 应用。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
origin.date: 01/23/2017
ms.date: 10/25/2018
ms.author: v-junlch
ROBOTS: NOINDEX
ms.openlocfilehash: 8a4eb5fe574e10e9ef7e476d1d7c3d16fcb4f988
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52655506"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>教程：识别图像中人脸的情感。

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](/cognitive-services/face/) 的一部分正式发布。 

探讨一个使用情感 API 来识别图像中人脸所表达的情感的基本 Windows 应用程序。 通过以下示例可提交图像 URL 或存储于本地的文件。 可以使用此开源示例作为模板，生成自己的使用情感 API 和 WPF（Windows Presentation Foundation，.NET Framework 的一部分）的 Windows 应用。

## <a name="Prerequisites">先决条件</a>
#### <a name="platform-requirements"></a>平台要求  
以下示例是使用 [Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) 针对 .NET Framework 开发的。  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>订阅情感 API 并获取订阅密钥  
在创建示例之前，必须订阅情感 API（Microsoft 认知服务的一部分）。 可以转到 [Azure 门户](https://portal.azure.cn)，并使用情感 API 创建一个认知服务来获取订阅密钥。 请务必遵循最佳做法，确保 API 密钥的机密性和安全性。  

#### <a name="get-the-client-library-and-example"></a>获取客户端库和示例  
可以通过 [SDK](https://www.github.com/microsoft/cognitive-emotion-windows) 下载情感 API 客户端库。 需将下载的 zip 文件解压缩到所选的文件夹，许多用户选择 Visual Studio 2015 文件夹。
## <a name="Step1">步骤 1：打开示例</a>
1.  启动 Microsoft Visual Studio 2015，单击“文件”，依次选择“打开”、“项目/解决方案”。
2.  浏览到下载的情感 API 文件所保存到的文件夹。 依次单击“情感”、“Windows”、“Sample-WPF”文件夹。
3.  双击名为 **EmotionAPI-WPF-Samples.sln** 的 Visual Studio 2015 解决方案 (.sln) 文件将其打开。 这会在 Visual Studio 中打开该解决方案。

## <a name="Step2">步骤 2：生成示例</a>
1. 在“解决方案资源管理器”中，右键单击“引用”，选择“管理 NuGet 包”。

    ![打开 Nuget 包管理器](../Images/EmotionNuget.png)

2.  此时会打开“NuGet 包管理器”窗口。 首先在左上角选择“浏览”，然后在搜索框中键入“Newtonsoft.Json”，选择 **Newtonsoft.Json** 包并单击“安装”。  

    ![浏览到 NuGet 包](../Images/EmotionNugetBrowse.png)  

3.  按 Ctrl+Shift+B，或者单击功能区菜单中的“生成”，并选择“生成解决方案”。

## <a name="Step3">步骤 3：运行示例</a>
1.  完成生成后，按 **F5**，或单击功能区菜单中的“开始”运行示例。
2.  找到“情感 API”窗口，其中包含带有“在此处粘贴订阅密钥以开始”字样的**文本框**。 如以下屏幕截图所示，在文本框中粘贴订阅密钥。 可单击“保存密钥”按钮，选择将订阅密钥保存在 PC 或笔记本电脑上。 若要从系统删除订阅密钥，请单击“删除密钥”将其从 PC 或笔记本电脑中删除。
  
    ![情感功能界面](../Images/EmotionKey.png)

3.  在“选择方案”下面，单击两种方案中的一种（“使用流检测情感”或“使用 URL 检测情感”），并遵照屏幕说明操作。 Microsoft 会收到所上传的图像，并可能会使用这些图像来改进情感 API 和相关服务。 提交图像即表示你已确认遵守我们的[开发人员行为准则](https://azure.microsoft.com/en-us/support/legal/developer-code-of-conduct/)。
4.  我们提供了可用于此示例应用程序的示例图像。 可以在[人脸 API Github 存储库](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)的“数据”文件夹下找到这些图像。 请注意，需根据“公平使用”协议的许可条款使用这些图像，这意味着，这些图像可用于测试此示例，但不可用于重新发布。

## <a name="Review">温故知新</a>
运行应用程序后，让我们了解此示例应用如何与 Microsoft 认知服务集成。 这样，便可以更轻松地使用 Microsoft 情感 API 在此应用中继续生成项目，或开发自己的应用。

此示例应用利用情感 API 客户端库 - Microsoft 情感 API 的精简 C# 客户端包装。 如上所述生成示例应用时，可以从 NuGet 包获取客户端库。 可以在“情感”>“Windows”>“客户端库”下面的标题为[客户端库](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)的文件夹（包含在根据前面[先决条件](#Prerequisites)所述下载的文件存储库中）中查看客户端库源代码。

还可以了解如何在“解决方案资源管理器”中使用客户端库代码：在“EmotionAPI-WPF_Samples”下面展开“DetectEmotionUsingStreamPage.xaml”，找到用于浏览到本地存储的文件的“DetectEmotionUsingStreamPage.xaml.cs”；或展开“DetectEmotionUsingURLPage.xaml”，找到上传图像 URL 时使用的“DetectEmotionUsingURLPage.xaml.cs”。 双击 .xaml.cs 文件，在 Visual Studio 的新窗口中将其打开。

让我们看看 **DetectEmotionUsingStreamPage.xaml.cs** 和 **DetectEmotionUsingURLPage.xaml.cs** 中的两个代码片段，了解情感客户端库在示例应用中的用法。 每个文件包含指出“KEY SAMPLE CODE STARTS HERE”和“KEY SAMPLE CODE ENDS HERE”的代码注释，可帮助找到下面重现的代码片段。

情感 API 可将图像 URL 或二进制图像数据（八位字节流格式）作为输入进行处理。 下面介绍了这两个选项。 在这种情况下，首先都要查找一条可让你使用情感客户端库的 using 指令。
```csharp
// ----------------------------------------------------------------------- 
// KEY SAMPLE CODE STARTS HERE 
// Use the following namespace for EmotionServiceClient 
// ----------------------------------------------------------------------- 
using Microsoft.ProjectOxford.Emotion; 
using Microsoft.ProjectOxford.Emotion.Contract; 
// ----------------------------------------------------------------------- 
// KEY SAMPLE CODE ENDS HERE 
// ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs

此代码片段演示如何使用客户端库将订阅密钥和照片 URL 提交到情感 API 服务。

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// -----------------------------------------------------------------------

window.Log("EmotionServiceClient is created");

//
// Create Project Oxford Emotion API Service client
//
EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
try
{
    //
    // Detect the emotions in the URL
    //
    Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
    return emotionResult;
}
catch (Exception exception)
{
    window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
    window.Log(exception.ToString());
    return null;
}
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs

下面演示了如何将订阅密钥和本地存储的图像提交到情感 API。


```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// -----------------------------------------------------------------------

//
// Create Project Oxford Emotion API Service client
//
EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
try
{
    Emotion[] emotionResult;
    using (Stream imageFileStream = File.OpenRead(imageFilePath))
    {
        //
        // Detect the emotions in the URL
        //
        emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
        return emotionResult;
    }
}
catch (Exception exception)
{
    window.Log(exception.ToString());
    return null;
}
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
