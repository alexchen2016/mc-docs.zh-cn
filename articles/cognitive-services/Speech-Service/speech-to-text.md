---
title: 有关语音转文本 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音转文本 API 将音频流转录为应用程序可以显示或作为输入操作的文本。 此服务通过 SDK 和 RESTful 终结点提供。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 02/08/2019
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: 711042257dae378f85b253578639a9f8780342df
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348422"
---
# <a name="about-the-speech-to-text-api"></a>关于语音转文本 API

**语音转文本** API 将音频流转录为应用程序可以向用户显示或作为命令输入操作的文本。 API 可以与 SDK 客户端库（用于支持的平台和语言）或 REST API 一起使用。

**语音转文本** API 提供以下功能：

- Microsoft 的高级语音识别技术 - 与 Cortana、Office 和其他 Microsoft 产品使用的一样。

- 实时连续识别。 **语音转文本**允许用户实时将音频转录为文本。 它还支持接收到目前为止已识别的单词的中间结果。 该服务自动识别结束语。 用户还可选择其他格式选项，包括大写和标点符号、脏话屏蔽和反向文本规范化。

- 结果以词法形式和显示形式返回（对于词法结果，请参阅示例或 API 中的 DetailedSpeechRecognitionResult）。

- 支持多种口语和方言。 有关每种识别模式下支持的语言的完整列表，请参阅[支持的语言](language-support.md#speech-to-text)。

- 自定义语言和声学模型，让你能够根据用户的专业领域词汇、演讲环境和演讲方式定制应用程序。


- 如果在语音配置对象（SpeechConfig.OutputFormat 属性）上指定详细输出，则会从服务返回置信度分数。 然后，可以对结果使用 Best() 方法，或者直接从服务返回的 JSON 获得分数（类似于 result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)）。

## <a name="api-capabilities"></a>API 功能

**语音转文本** API 的某些功能（特别是围绕自定义的功能）可以通过 REST 获得。 下表总结了每种访问 API 的方法的功能。 有关功能和 API 详细信息的完整列表，请参阅 [Swagger 参考](https://westus.cris.ai/swagger/ui/index)。

| 使用案例 | REST | SDK |
|-----|-----|-----|----|
| 转录简短的陈述，如命令（长度 < 15 秒）；没有中期结果 | 是 | 是 |
| 转录较长的陈述（> 15 秒） | 否 | 是 |
| 使用可选的中间结果转录流式处理音频 | 否 | 是 |

| 创建准确度测试 | 是 | 否 | | 上传进行模型适应的数据集 | 是 | 否 | | 创建并管理语音模型 | 是 | 否 | | 创建并管理模型部署 | 是 | 否 | | 管理订阅 | 是 | 否 | | 创建并管理模型部署 | 是 | 否 | | 创建并管理模型部署 | 是 | 否 |

> [!NOTE]
> REST API 实现限制，将 API 请求限制为每 5 秒 25 次。 消息标头将通知限制


## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://www.azure.cn/zh-cn/home/features/cognitive-services/)
* [快速入门：在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)

