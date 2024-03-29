---
title: 快速入门：识别语音，JavaScript（浏览器）- 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在浏览器中使用语音 SDK 通过 JavaScript 识别语音
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
origin.date: 2/20/2019
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 53c11132ae7e62f34d9edd4c7ecf5a4429de7b17
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627612"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>快速入门：在浏览器中使用语音 SDK 通过 JavaScript 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何创建使用认知服务语音 SDK 的 JavaScript 绑定以将语音转录为文本的网站。
应用程序基于 Microsoft 认知服务语音 SDK（[下载版本 1.3.0](https://aka.ms/csspeech/jsbrowserpackage)）。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[试用语音服务](get-started.md)。
* 配有可正常工作的麦克风的电脑或 Mac。
* 文本编辑器。
* Microsoft Edge 的当前版本。
* （可选）支持承载 PHP 脚本的 web 服务器。

## <a name="create-a-new-website-folder"></a>新建网站文件夹

新建空文件夹。 如果要在 web 服务器上承载示例，请确保 web 服务器可访问文件夹。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>将 JavaScript 的语音 SDK 解压缩到文件夹

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

将语音 SDK 作为 [.zip 包](https://aka.ms/csspeech/jsbrowserpackage)下载，并将其解压缩到新建文件夹。 这导致两个文件（`microsoft.cognitiveservices.speech.sdk.bundle.js` 和 `microsoft.cognitiveservices.speech.sdk.bundle.js.map`）被解压缩。
后一个文件是可选的，可用于调试到 SDK 代码中。

## <a name="create-an-indexhtml-page"></a>创建 index.html 页面

在文件夹中创建名为 `index.html` 的新文件，使用文本编辑器打开此文件。

1. 创建以下 HTML 框架：

   ```html
   <html>
   <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

2. 将以下 UI 代码添加到文件，以下是第一个注释：

   ```html
   <div id="warning">
     <h1 style="font-weight:500;">Speech Recognition Speech SDK not found (microsoft.cognitiveservices.speech.sdk.bundle.js missing).</h1>
   </div>

   <div id="content" style="display:none">
     <table width="100%">
       <tr>
         <td></td>
         <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
        </tr>
        <tr>
         <td align="right"><a href="https://docs.azure.cn/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
         <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
        </tr>
        <tr>
         <td align="right">Region</td>
         <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
        </tr>
        <tr>
         <td></td>
         <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
        </tr>
        <tr>
         <td align="right" valign="top">Results</td>
         <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
        </tr>
      </table>
   </div>
   ```

3. 添加对语音 SDK 的引用

   ```HTML
   <!-- Speech SDK reference sdk. -->
   <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>
   ```

4. 连接“识别”按钮、识别结果和 UI 代码定义的订阅相关字段的处理程序：

   ```HTML
   <!-- Speech SDK USAGE -->
   <script>
     // status fields and start button in UI
     var phraseDiv;
     var startRecognizeOnceAsyncButton;

     // subscription key and region for speech services.
     var subscriptionKey, serviceRegion;
     var authorizationToken;
     var SpeechSDK;
     var recognizer;

     document.addEventListener("DOMContentLoaded", function () {
     startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
     subscriptionKey = document.getElementById("subscriptionKey");
     serviceRegion = document.getElementById("serviceRegion");
     phraseDiv = document.getElementById("phraseDiv");

     startRecognizeOnceAsyncButton.addEventListener("click", function () {
     startRecognizeOnceAsyncButton.disabled = true;
     phraseDiv.innerHTML = "";

      // if we got an authorization token, use the token. Otherwise use the provided subscription key
      var speechConfig;
      if (authorizationToken) {
        speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
       } else {
        if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
          alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
          return;
        }
        speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
       }

       speechConfig.speechRecognitionLanguage = "en-US";
       var audioConfig  = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
       recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);

       recognizer.recognizeOnceAsync(
        function (result) {
          startRecognizeOnceAsyncButton.disabled = false;
          phraseDiv.innerHTML += result.text;
          window.console.log(result);

          recognizer.close();
          recognizer = undefined;
        },
        function (err) {
          startRecognizeOnceAsyncButton.disabled = false;
          phraseDiv.innerHTML += err;
          window.console.log(err);

          recognizer.close();
          recognizer = undefined;
        });
    });

    if (!!window.SpeechSDK) {
      SpeechSDK = window.SpeechSDK;
      startRecognizeOnceAsyncButton.disabled = false;

      document.getElementById('content').style.display = 'block';
      document.getElementById('warning').style.display = 'none';

      // in case we have a function for getting an authorization token, call it.
      if (typeof RequestAuthorizationToken === "function") {
          RequestAuthorizationToken();
       }
      }
    });
   </script>
   ```

## <a name="create-the-token-source-optional"></a>创建令牌源（可选）

如果要在 web 服务器上承载网页，可以为演示应用程序提供令牌源。
这样一来，订阅密钥永远不会离开服务器，并且用户可以在不输入任何授权代码的情况下使用语音功能。

1. 创建名为 `token.php` 的新文件。 此示例假设 web 服务器支持 PHP 脚本语言。 输入以下代码：

   ```PHP
   <?php
   header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

   // Replace with your own subscription key and service region.
   $subscriptionKey = 'YourSubscriptionKey';
   $region = 'YourServiceRegion';

   $ch = curl_init();
   curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
   curl_setopt($ch, CURLOPT_POST, 1);
   curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
   curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey)); 
   curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
   echo curl_exec($ch);
   ?>
   ```

1. 编辑 `index.html` 文件，将以下代码添加到文件：

```HTML
<!-- Speech SDK Authorization token -->
<script>
// Note: Replace the URL with a valid endpoint to retrieve
//       authorization tokens for your subscription.
var authorizationEndpoint = "token.php";

function RequestAuthorizationToken() {
  if (authorizationEndpoint) {
    var a = new XMLHttpRequest();
    a.open("GET", authorizationEndpoint);
    a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    a.send("");
    a.onload = function() {
        var token = JSON.parse(atob(this.responseText.split(".")[1]));
        serviceRegion.value = token.region;
        authorizationToken = this.responseText;
        subscriptionKey.disabled = true;
        subscriptionKey.value = "using authorization token (hit F5 to refresh)";
        console.log("Got an authorization token: " + token);
    }
  }
}
</script>
```

> [!NOTE]
> 授权令牌仅具有有限的生存期。
> 此简化示例不显示如何自动刷新授权令牌。 作为用户，你可以手动重载页面或点击 F5 刷新。

## <a name="build-and-run-the-sample-locally"></a>在本地生成和运行示例

要启动应用，双击 index.html 文件或使用你喜欢的 web 浏览器打开 index.html。 随即显示的简单 GUI 允许你输入订阅密钥和[区域](regions.md)以及使用麦克风触发识别。

## <a name="build-and-run-the-sample-via-a-web-server"></a>通过 web 服务器生成并运行示例

要启动应用，打开你最喜欢的 web 浏览器，将其指向承载文件夹的公共 URL，输入你的[区域](regions.md)，使用麦克风触发识别。 配置后，它将获取令牌源中的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 JavaScript 示例](https://aka.ms/csspeech/samples)
