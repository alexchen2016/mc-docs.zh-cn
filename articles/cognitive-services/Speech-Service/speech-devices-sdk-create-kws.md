---
title: 创建自定义唤醒词 - 语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终在侦听唤醒词（或短语）。 当用户说唤醒词时，设备会将所有后续音频发送到云，直到用户停止说话为止。 自定义唤醒词是区分设备和加强品牌效应的有效方式。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: a12e82b97039f370b24618df814a18d820fcc1ca
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348253"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>使用语音服务创建自定义唤醒词

设备始终在侦听唤醒词（或短语）。 例如，“Hey Cortana”是 Cortana 助手的唤醒词。 当用户说唤醒词时，设备会将所有后续音频发送到云，直到用户停止说话为止。 自定义唤醒词是区分设备和加强品牌效应的有效方式。

本文介绍如何为设备创建自定义唤醒词。

## <a name="choose-an-effective-wake-word"></a>选择有效的唤醒词

选择唤醒词时请考虑以下准则：

* 唤醒词应该是英文单词或短语。 说该词应该不超过两秒钟。

* 4 到 7 个音节的单词效果最好。 例如，“Hey, Computer”是一个很好的唤醒词， 而只是“Hey”则是一个糟糕的唤醒词。

* 唤醒词应遵循常见的英语发音规则。

* 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，“computerama”可能是一个很好的唤醒词。

* 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。

* 避免使用可能有其他发音的唤醒词。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。

* 不要使用特殊字符、符号或数字。 例如，“Go#”和“20 + cats”不会是好的唤醒词。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择商标词作为唤醒词，请确保你拥有该商标，或者获得商标所有者的许可才能使用该词。 Microsoft 对你选择的唤醒词可能引起的任何法律问题不承担任何责任。

## <a name="create-your-wake-word"></a>创建唤醒词

在设备上使用自定义唤醒词之前，必须先使用 Microsoft 自定义唤醒词生成服务来创建唤醒词。 你提供唤醒词后，该服务会生成一个文件，由你将该文件部署到开发工具包中，以便在设备上启用唤醒词。

1. 转到[自定义语音服务门户](https://cris.ai/)。

1. 使用你收到 Azure Active Directory 邀请的电子邮件地址创建一个新帐户。

    ![创建新帐户](media/speech-devices-sdk/wake-word-1.png)

1. 公众无法使用“自定义唤醒词”页，因此没有直接链接可以将你带到那里。 自定义语音功能需要 Azure 订阅，但自定义唤醒词功能不需要。 如果显示“找不到任何订阅。” 错误页，请直接在 URL 中将“Subscriptions?errorMessage=No%20Subscriptions%20found”替换为“customkws”，然后按 Enter。 该 URL 应该是 https://westus.cris.ai/customkws、 https://eastasia.cris.ai/customkws、 https://northeurope.cris.ai/customkws 中的一个，具体取决于你所在的区域。

    ![“自定义唤醒词”页处于隐藏状态](media/speech-devices-sdk/wake-word-4.png)

1. 键入所选的唤醒词，然后选择“提交唤醒词”。

    ![输入唤醒词](media/speech-devices-sdk/wake-word-5.png)

1. 可能需要几分钟才能生成文件。 此时会在浏览器窗口中看到一个旋转的圆圈。 过了一会儿，会出现一个信息栏，要求你下载 .zip 文件。

    ![接收 .zip 文件](media/speech-devices-sdk/wake-word-6.png)

1. 将 .zip 文件保存到计算机。 你需要此文件才能将自定义唤醒词部署到开发工具包。 若要部署自定义唤醒词，请按照[语音设备 SDK 入门](speech-devices-sdk-qsg.md)中的说明操作。

1. 选择“注销”。

## <a name="next-steps"></a>后续步骤

若要入门，请获取[试用版 Azure 帐户](https://www.azure.cn/zh-cn/home/features/cognitive-services/)并注册语音设备 SDK。

> [!div class="nextstepaction"]
> [注册语音设备 SDK](get-speech-devices-sdk.md)
