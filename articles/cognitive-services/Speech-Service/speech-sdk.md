---
title: 关于语音 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音软件开发工具包 (SDK)，应用程序可以本机访问语音服务的功能，这使得软件开发工作更为容易。 本文提供了有关适用于 Windows、Linux 和 Android 的 SDK 的其他详细信息。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 2/20/2019
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: b1ddee9c3667ec97c31d09ee3b5004a1c7af83d8
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348364"
---
# <a name="about-the-speech-sdk"></a>关于语音 SDK

使用语音软件开发工具包 (SDK)，应用程序可以本机访问语音服务的功能，这使得软件开发工作更为容易。 当前，该 SDK 提供了对**语音到文本**、**语音翻译**和**意向识别**功能的访问。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>获取 SDK

### <a name="windows"></a>Windows

对于 Windows，我们支持以下语言：

* C#（UWP 和 .NET）、C++：可以引用和使用语音 SDK NuGet 包的最新版本。 此包包括 32 位和 64 位客户端库，以及托管 (.NET) 库。 可以使用 NuGet 在 Visual Studio 中安装该 SDK。 搜索 Microsoft.CognitiveServices.Speech NuGet。

* Java:可以引用和使用语音 SDK Maven 包的最新版本，该包仅支持 Windows x64。 在 Maven 项目中，将 `https://csspeechstorage.blob.core.chinacloudapi.cn/maven/` 添加为附加存储库，并将 `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` 引用为一个依赖项。

### <a name="linux"></a>Linux

> [!NOTE]
> 目前，我们在 PC（用于 C++ 开发的 x86 或 x64，用于 .NET Core、Java 和 Python 的 x64）上仅支持 Ubuntu 16.04 和 18.04。

通过运行以下 shell 命令确保你安装了必需的编译器和库：

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

* C#：可以引用和使用语音 SDK NuGet 包的最新版本。 若要引用该 SDK，请向你的项目中添加以下包引用：

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.3.1" />
  ```

* Java:可以引用和使用语音 SDK Maven 包的最新版本。 在 Maven 项目中，将 `https://csspeechstorage.blob.core.chinacloudapi.cn /maven/` 添加为附加存储库，并将 `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` 引用为一个依赖项。

* C++：将 SDK 下载为 [.tar 包](https://aka.ms/csspeech/linuxbinary)，并将文件解压缩到所选的一个目录中。 下表显示了 SDK 文件夹结构：

  |路径|说明|
  |-|-|
  |`license.md`|许可|
  |`ThirdPartyNotices.md`|第三方声明|
  |`include`|用于 C 和 C++ 的头文件|
  |`lib/x64`|用于与应用程序链接的本机 x64 库|
  |`lib/x86`|用于与应用程序链接的本机 x86 库|

  要创建应用程序，请将必需的二进制文件（以及库）复制到开发环境中。 在生成过程中根据需要添加它们。

### <a name="android"></a>Android

将适用于 Android 的 Java SDK 打包为 [AAR（Android 库）](https://developer.android.com/studio/projects/android-library)，其内附必要的库以及所需的 Android 权限。 它作为包 `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` 托管在 `https://csspeechstorage.blob.core.chinacloudapi.cn /maven/` 的 Maven 存储库中。

若要从你的 Android Studio 项目中使用该包，请进行以下更改：

* 在项目级 build.gradle 文件中，向 `repository` 部分添加以下内容：

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.chinacloudapi.cn /maven/' }
  ```

* 在模块级 build.gradle 文件中，向 `dependencies` 部分添加以下内容：

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.3.1'
  ```

Java SDK 也是[语音设备 SDK](speech-devices-sdk.md) 的一部分。

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://www.azure.cn/zh-cn/home/features/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
