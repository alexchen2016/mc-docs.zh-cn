---
title: 有关语音设备 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门。 语音服务适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 12/06/2018
ms.author: v-biyu
ms.custom: 04/01/2019
ms.openlocfilehash: 7a5ed74561a6b8beb4a4a0e4dcdc42594b091de6
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348249"
---
# <a name="about-the-speech-devices-sdk-preview"></a>关于语音设备 SDK（预览）

[语音服务](overview.md)适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。

语音设备 SDK 有助于：
* 快速测试新的语音方案。
* 更轻松地将基于云的语音服务集成到设备中。
* 为客户创建出色的用户体验。

语音设备 SDK 使用[语音 SDK](speech-sdk.md)。 它使用语音 SDK 将我们的高级音频处理算法处理的音频从设备的麦克风阵列发送到[语音服务](overview.md)。 它使用多声道音频，通过噪声抑制、回音消除、波束赋形和残响消除来提供更准确的远场[语音识别](speech-to-text.md)。

你还可以使用语音设备 SDK 来构建具有你自己的[自定义唤醒字](speech-devices-sdk-create-kws.md)的环境设备 - 因此启动用户交互的提示词将为品牌特有。

该语音设备 SDK 支持多种启用语音的方案，例如免下车点单系统、店内或家用助理和智能扬声器。 可以使用文本回复用户、用默认或[自定义语音](how-to-customize-voice-font.md)回答用户、提供搜索结果以及[翻译](speech-translation.md)为其他语言等。 我们期待看到你的成果！

## <a name="development-kit-providers"></a>开发工具包提供程序

目前，这些完整的端到端系统参考设计都已发布：

|||
|-|-|
|[![ROOBO 徽标](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO 提供面向家用电器、汽车、机器人、玩具和其他行业的完整人工智能 (AI) 系统解决方案。 通过与 Microsoft 语音服务集成，ROOBO 的参考设计大大缩短了从开发到上市的时间。 [访问 ROOBO](http://ddk.roobo.com/)。|

## <a name="next-steps"></a>后续步骤

若要入门，请获取[试用版 Azure 帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)并注册语音设备 SDK。

> [!div class="nextstepaction"]
> [注册语音设备 SDK](get-speech-devices-sdk.md)
