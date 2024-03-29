---
title: 什么是语音服务？
titleSuffix: Azure Cognitive Services
description: 语音服务是 Azure 认知服务的一部分，它将以前单独提供的几种语音服务结合在一起：必应语音（其中包括语音识别和文本转语音）、自定义语音和语音翻译。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
origin.date: 12/13/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 11cdf9c77b75784f51d1bc05bf60c01b391f9094
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348497"
---
# <a name="what-is-speech-services"></a>什么是语音服务？

与其他 Azure 语音服务一样，语音服务由在 Cortana 和 Microsoft Office 之类的产品中使用的语音技术提供支持。

语音服务合并了此前通过必应语音 API、语音翻译、[自定义语音](https://docs.azure.cn/zh-cn/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home)和自定义声音服务提供的 Azure 语音功能。 现在，可以通过一个订阅来访问所有这些功能。

## <a name="main-speech-services-functions"></a>语音服务的主要功能

语音服务的主要功能为语音转文本（也称为语音识别或听录）、文本转语音（语音合成）和语音翻译。

|函数|功能|
|-|-|
|[语音转文本](speech-to-text.md)| <li>将连续的实时语音转换为文本。<li>可以从音频录音对语音进行批量转换。 <li>支持中间结果、语音结束检测、自动设置文本格式以及不雅内容屏蔽。\*|
|[文本转语音](text-to-speech.md)| <li>**新增功能**：提供神经文本转语音的声音，几乎与人类语音无法区分（英语）。 <li>将文本转换为自然发音的语音。 <li>为许多支持的语言提供多种性别和/或方言。 <li>支持纯文本输入或语音合成标记语言 (SSML)。 |
|[语音翻译](speech-translation.md)| <li>以近实时方式翻译流式传输音频。<li> 也可处理记录的语音。<li>以文本或合成语音的方式提供结果。 |


## <a name="customize-speech-features"></a>自定义语音功能

可以使用自己的数据来训练语音服务的语音转文本和文本转语音功能下的模型。

|功能|型号|目的|
|-|-|-|
|语音转文本|[声学模型](how-to-customize-acoustic-models.md)|有助于转换特定发声对象和环境，例如汽车或工厂。|
||[语言模型](how-to-customize-language-model.md)|有助于转换特定于领域的词汇和语法，例如医学或 IT 专业术语。|
||[发音模型](how-to-customize-pronunciation.md)|有助于转换缩写和首字母缩写，例如“IOU”是“i owe you”的首字母缩写。 |
|文本转语音|[语音字体](how-to-customize-voice-font.md)|根据人类语音的示例来训练模型，为应用提供它自己的语音。|

自定义模型可以用在任何位置，只要可以在该位置使用应用的语音转文本或文本转语音功能中的标准模型。

## <a name="use-the-speech-service"></a>使用语音服务

为了简化支持语音的应用程序的开发，Azure 提供了可以与语音服务配合使用的[语音 SDK](speech-sdk.md)。 语音 SDK 提供一致的本机语音转文本和语音翻译 API，适用于 C#、C++ 和 Java。 如果使用这其中的某种语言进行开发，你会发现语音 SDK 有助于开发，因为它可以为你处理网络详细信息。

语音服务还有一个 [REST API](rest-apis.md)，此 API 适用于任何能够发出 HTTP 请求的编程语言。 此 REST 接口不提供 SDK 的流式处理实时功能。

|<br>方法|语音<br>转文本|文本转<br>语音|语音<br>翻译|<br>说明|
|-|-|-|-|-|
|[语音 SDK](speech-sdk.md)|是|否|是|适用于 C#、C++ 和 Java 的本机 API，可以简化开发。|
|[REST API](rest-apis.md)|是|是|否|一个基于 HTTP 的简单 API，可以轻松地将语音添加到应用程序。|

### <a name="websockets"></a>WebSockets

语音服务还支持 WebSocket 协议，用于流式传输语音转文本和语音翻译内容。 语音 SDK 使用这些协议与语音服务通信。 使用语音 SDK，而不是尝试实现你自己与语音服务的 WebSocket 通信。

如果已经有可以通过 WebSocket 使用必应语音或语音翻译的代码，则可更新代码以使用语音服务。 WebSocket 协议是兼容的，但是终结点不同。

### <a name="speech-devices-sdk"></a>语音设备 SDK

[语音设备 SDK](speech-devices-sdk.md) 是一种集成式硬件和软件平台，适用于支持语音的设备的开发人员。 我们的硬件合作伙伴提供参考设计和开发单元。 Azure 提供设备优化的 SDK，该 SDK 可以充分利用硬件的功能。


## <a name="speech-scenarios"></a>语音方案

语音服务用例包括：

> [!div class="checklist"]
> * 创建语音触发的应用
> * 转录呼叫中心录制内容
> * 实现语音机器人

### <a name="voice-user-interface"></a>语音用户界面

语音输入是使应用变得灵活、免持和快速可用的极佳办法。 使用支持语音的应用，用户只需请求所需的信息即可。

如果应用面向公众，可以使用默认语音识别模型。 此类模型能够识别常规环境中的各种发声对象。

如果在特定领域（例如医学或 IT）中使用应用，可以创建[语言模型](how-to-customize-language-model.md)。 可以使用此模型向语音服务传授应用使用的特殊术语。

如果在噪音环境（例如工厂）中使用应用，可以创建自定义[声学模型](how-to-customize-acoustic-models.md)。 此模型可帮助语音服务将语音与噪音区分开来。

### <a name="call-center-transcription"></a>呼叫中心听录

通常，仅当来电中提出了某个问题时，才会查阅呼叫中心的录制内容。 借助语音服务，可以轻松将所有录制内容转录为文本。 可以轻松编制索引，或应用[文本分析](https://docs.azure.cn/cognitive-services/Text-Analytics/)来检测情绪、语言和关键短语。

如果呼叫中心录制内容使用了专业术语（例如产品名称或 IT 行话），则可创建一个[语言模型](how-to-customize-language-model.md)来向语音服务传授这些词汇。 自定义[声学模型](how-to-customize-acoustic-models.md)可帮助语音服务理解欠佳的电话连接。

有关此方案的详细信息，请详细阅读语音服务的[批处理脚本](batch-transcription.md)。

### <a name="voice-bots"></a>语音机器人

[机器人](https://dev.botframework.com/)是让用户接触到他们所需的信息，或者让客户接触到他们所热衷的业务的一种流行方式。 将聊天用户界面添加到网站或应用后，可更轻松地查找和更快地访问其功能。 借助语音服务，这种聊天能以全新的流畅度进行，能够对语音查询进行语音响应。

若要将独特的个性化特点添加到支持语音的机器人，可在机器人中提供其自己的语音。 创建自定义语音的过程包括两个步骤。 首先[录制](record-custom-voice-samples.md)想要使用的语音。 然后，[提交这些录制内容](how-to-customize-voice-font.md)和文本脚本到语音服务的语音自定义门户，后者会完成剩余的工作。 创建自定义语音后，在应用中使用该语音的步骤就非常简单了。

## <a name="next-steps"></a>后续步骤

获取语音服务的订阅密钥。

> [!div class="nextstepaction"]
> [试用语音服务](get-started.md)
