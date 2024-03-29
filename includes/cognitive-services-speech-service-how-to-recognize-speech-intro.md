---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: d4b31ceaed88fdd809bd0b7657f6e41da284c566
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348288"
---
<!-- N.B. no header, no intents here, language-agnostic -->

认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供在具有功能完整的应用程序中使用“语音转文本”的最简单方法。

1. 创建语音配置，并提供语音服务订阅密钥（或授权令牌）及[区域](~/articles/cognitive-services/speech-service/regions.md)作为参数。 根据需要更改配置。 例如，提供自定义终结点来指定非标准服务终结点，或选择语音输入语言或输出格式。

1. 根据语音配置创建语音识别器。 若要从除默认麦克风之外的来源（例如，音频流或音频文件）识别语音，请提供音频配置。

1. 如果需要，可捆绑事件以进行异步操作。 然后，识别器将在其具有中间和最终结果时调用事件处理程序。 否则，应用程序将只收到最终听录结果。

1. 开始识别。 对于单次识别（例如命令或查询识别），请使用 `RecognizeOnceAsync()`（或语言等效）方法。 此方法返回第一个已识别的话语。 对于长时间运行的识别（例如听录），请使用 `StartContinuousRecognitionAsync()`（或语言等效）方法。 捆绑事件以获得异步识别结果。

有关使用语音 SDK 的语音识别方案，请参阅以下代码片段。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
