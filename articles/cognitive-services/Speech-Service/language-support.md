---
title: 语言支持 - 语音服务 API
titleSuffix: Azure Cognitive Services
description: Azure 语音服务支持多种语言，可用于语音到文本和文本到语音转换，以及语音翻译。 本文提供服务支持语言的完整列表。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 12/13/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: d29d4ea508a70b85c46c48ac26e1e0dc87dc7a73
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348348"
---
# <a name="language-and-region-support-for-speech-service-api"></a>语音服务 API 的语言和区域支持

不同的语音服务函数支持不同的语言。 下表汇总了语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft 语音识别 API 支持以下语言。 每种语言都有不同级别的自定义。

  代码 | 语言 | [声学适应](how-to-customize-acoustic-models.md) | [语言适应](how-to-customize-language-model.md) | [发音适应](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | 阿拉伯语(埃及)，现代标准 | 否 | 是 | 否
 ca-ES | 加泰罗尼亚语(西班牙) | 否 | 否 | 否
 da-DK | 丹麦语(丹麦) | 否 | 否 | 否
 de-DE | 德语(德国) | 是 | 是 | 否
 en-AU | 英语(澳大利亚) | 否 | 是 | 是
 en-CA | 英语(加拿大) | 否 | 是 | 是
 en-GB | 英语(英国) | 否 | 是 | 是
 en-IN | 英语(印度) | 是 | 是 | 是
 en-NZ | 英语(新西兰) | 否 | 是 | 是  
 en-US | 英语(美国) | 是 | 是 | 是
 es-ES | 西班牙语(西班牙) | 是 | 是 | 否
 es-MX | 西班牙语(墨西哥) | 否 | 是 | 否
 fi-FI | 芬兰语(芬兰) | 否 | 否 | 否
 fr-CA | 法语(加拿大) | 否 | 是 | 否
 fr-FR | 法语(法国) | 是 | 是 | 否
 hi-IN | 印地语(印度) | 否 | 是 | 否
 it-IT | 意大利语(意大利) | 是 | 是 | 否
 ja-JP | 日语(日本) | 否 | 是 | 否
 ko-KR | 韩语(韩国) | 否 | 是 | 否
 nb-NO | 书面挪威语(挪威) | 否 | 否 | 否
 nl-NL | 荷兰语(荷兰) | 否 | 是 | 否
 pl-PL | 波兰语(波兰) | 否 | 否 | 否
 pt-BR | 葡萄牙语(巴西) | 是 | 是 | 否
 pt-PT | 葡萄牙语(葡萄牙) | 否 | 是 | 否
 ru-RU | 俄语(俄罗斯) | 是 | 是 | 否
 sv-SE | 瑞典语(瑞典) | 否 | 否 | 否
 zh-CN | 中文(普通话，简体) | 是 | 是 | 否
 zh-HK | 中文(普通话，繁体) | 否 | 是 | 否
 zh-TW | 中文(台湾普通话) | 否 | 是 | 否
 th-TH | 泰语(泰国) | 否 | 否 | 否


## <a name="text-to-speech"></a>文本转语音

文本转语音 REST API 支持以下语音，每种语音支持区域设置标识的特定语言和方言。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的定价各不相同。 有关其他信息，请访问[定价](https://www.azure.cn/zh-cn/home/features/cognitive-services/)页。

### <a name="neural-voices-preview"></a>神经语音（预览版）

神经文本到语音转换是由深度神经网络提供支持的新型语音合成。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。

可以使用神经语音使与聊天机器人和虚拟助手的交流更加自然和专注、可将数字文本（如电子书）转换为有声读物以及可增强车载导航系统。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

有关神经语音和区域可用性的完整列表，请参阅[区域](regions.md#neural-voices)。

| 区域设置 | 语言 | 性别 | 服务名称映射|
|--------|----------|---------|--------------------|
| en-US | 英语(美国) | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" |
| en-US | 英语(美国) | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" |
| zh-CN | 中文 | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" |

> [!IMPORTANT]
> Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural) 仅可通过以下东南亚终结点获得： https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1 。

### <a name="standard-voices"></a>标准语音

提供超过 75 种标准语音，涉及超过 45 种语言和区域设置，允许用户将文本转换为合成语音。 有关区域可用性的详细信息，请参阅[区域](regions.md#standard-voices)。

区域设置 | 语言 | 性别 | 服务名称映射
-------|----------|---------|--------------------
ar-EG\* | 阿拉伯语(埃及) | 女 | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | 阿拉伯语(沙特阿拉伯) | 男 | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | 保加利亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | 加泰罗尼亚语(西班牙) | 女 | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | 捷克语 | 男 | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | 丹麦语 | 女 | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | 德语(奥地利) | 男 | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | 德语(瑞士) | 男 | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | 德语(德国) | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)"
el-GR | 希腊语 | 男 | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | 英语(澳大利亚) | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | 英语(加拿大) | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | 英语(英国) | 女 | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | 英语(爱尔兰) |男 | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | 英语(印度) | 女 | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
en-US | 英语(美国) |女 | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"
es-ES | 西班牙语(西班牙) |女 | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | 西班牙语(墨西哥) | 女 | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | 芬兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | 法语(加拿大) |女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | 法语(瑞士)|男 | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | 法语(法国)|女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| 希伯来语(以色列) | 男| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | 印地语(印度) | 女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | 克罗地亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | 匈牙利语 | 男 | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | 印度尼西亚语| 男 | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | 意大利语 |男 | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)"
ja-JP | 日语 |女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
ko-KR | 韩语 |女 | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | 马来语 | 男 | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | 挪威语 | 女 | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | 荷兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | 波兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | 葡萄牙语(巴西) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | 葡萄牙语(葡萄牙) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | 罗马尼亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU |俄语| 女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)"
sk-SK | 斯洛伐克语|男 | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | 斯洛文尼亚语|男 | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | 瑞典语|女 | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | 泰米尔语(印度) |男 | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
te-IN | 泰卢固语(印度) |女 | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)"
th-TH | 泰语|男 | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR |土耳其语| 女 | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | 越南语|男 | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | 中文(中国)|女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
| | |男 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | 中文(香港特别行政区)|女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
| | |女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
| || 男 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | 中文(中国台湾)|女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
| || 女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
| || 男 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"

\* *ar-EG 支持现代标准阿拉伯语 (MSA)。*

### <a name="customization"></a>自定义

语音自定义功能适用于美国英语 (en-US)、中国大陆的普通话 (zh-CN)、法语 (fr-FR)、德语 (de-DE) 和意大利语 (it-IT)。

> [!NOTE]
> 法语、德语和意大利语音训练从包含 2,000 多条话语的数据集开始。 包含 2,000 多条话语的初始数据集也支持中英双语模型。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自“语音转文本”语言表。 可用的目标语言取决于翻译目标是语音还是文本。 可以将传入的语音翻译成 60 种以上的语言。 这些语言的子集可用于[语音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文本语言

| 文本语言    | 语言代码 |
|:----------- |:-------------:|
| 南非荷兰语      | `af`          |
| 阿拉伯语       | `ar`          |
| Bangla      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 粤语(繁体)      | `yue`          |
| 加泰罗尼亚语      | `ca`          |
| 中文(简体)      | `zh-Hans`          |
| 中文(繁体)      | `zh-Hant`          |
| 克罗地亚语      | `hr`          |
| 捷克语      | `cs`          |
| 丹麦语      | `da`          |
| 荷兰语      | `nl`          |
| 英语      | `en`          |
| 爱沙尼亚语      | `et`          |
| 斐济语      | `fj`          |
| 菲律宾语      | `fil`          |
| 芬兰语      | `fi`          |
| 法语      | `fr`          |
| 德语      | `de`          |
| 希腊语      | `el`          |
| 海地克里奥尔语      | `ht`          |
| 希伯来语      | `he`          |
| 印地语      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利语      | `hu`          |
| 印度尼西亚语      | `id`          |
| 意大利语      | `it`          |
| 日语      | `ja`          |
| 斯瓦希里语      | `sw`          |
| 克林贡语      | `tlh`          |
| 克林贡语(plqaD)      | `tlh-Qaak`          |
| 韩语      | `ko`          |
| 拉脱维亚语      | `lv`          |
| 立陶宛语      | `lt`          |
| 马达加斯加语      | `mg`          |
| 马来语      | `ms`          |
| 马耳他语      | `mt`          |
| 挪威语      | `nb`          |
| 波斯语      | `fa`          |
| 波兰语      | `pl`          |
| 葡萄牙语      | `pt`          |
| 克雷塔罗奥托米语      | `otq`          |
| 罗马尼亚语      | `ro`          |
| 俄语      | `ru`          |
| 萨摩亚语      | `sm`          |
| 塞尔维亚语(西里尔文)      | `sr-Cyrl`          |
| 塞尔维亚语(拉丁语系)      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 塔希提语      | `ty`          |
| 泰米尔语      | `ta`          |
| 泰语      | `th`          |
| 汤加语      | `to`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 乌尔都语      | `ur`          |
| 越南语      | `vi`          |
| 威尔士语      | `cy`          |
| 尤卡坦玛雅语      | `yua`          |


## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://www.azure.cn/zh-cn/home/features/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
