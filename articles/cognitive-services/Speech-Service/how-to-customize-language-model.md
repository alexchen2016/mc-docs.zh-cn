---
title: 教程：如何使用语音服务创建语言模型
titlesuffix: Azure Cognitive Services
description: 了解如何使用语音服务创建语言模型。 将此自定义语言模型与 Microsoft 提供的现成的最新语音模型配合使用，以便向应用程序添加语音交互功能。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
origin.date: 12/06/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: 2ce677a339b7835e796a9ce6bdee2242bd3edb3d
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348385"
---
# <a name="tutorial-create-a-custom-language-model"></a>教程：创建自定义语言模型

在本文档中，我们将创建一个自定义语言模型。 然后即可将此自定义语言模型与 Microsoft 提供的现成的最新语音模型配合使用，以便向应用程序添加语音交互功能。

本文档介绍如何以下操作：
> [!div class="checklist"]
> * 准备数据
> * 导入语言数据集
> * 创建自定义语言模型

如果没有认知服务帐户，请在开始前创建一个[试用帐户](https://www.azure.cn/zh-cn/home/features/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

为了确保认知服务帐户已连接到某个订阅，请打开[认知服务订阅](https://customspeech.ai/Subscriptions)页。

若要连接到在 Azure 门户中创建的语音服务订阅，请选择“连接现有的订阅”按钮。

有关在 Azure 门户中创建语音服务订阅的信息，请参阅[入门](get-started.md)页。

## <a name="prepare-the-data"></a>准备数据

若要为应用程序创建自定义语言模型，需为系统提供一系列示例话语，例如：

*   "The patient has had urticaria for the past week."
*   "The patient had a well-healed herniorrhaphy scar."

这些句子不需要是完整或语法正确的句子，但应准确反映系统在部署过程中通常会遇到的语音输入。 这些示例应该反映用户将通过应用程序执行的任务的样式和内容。

语言模型数据应以 UTF-8 BOM 编码写入。 此文本文件应该每行包含一个示例（句子、陈述或查询）。

如果希望某些字词的权重（重要性）更高，可将包含这些字词的多个话语添加到数据。

下表汇总了语言数据的主要要求。

| 属性 | 值 |
|----------|-------|
| 文本编码 | UTF-8 BOM|
| 每行的话语数 | 1 |
| 文件大小上限 | 1.5 GB |
| 备注 | 避免将字符重复 4 次以上，例如“aaaaa”|
| 备注 | 不得使用“\t”之类的特殊字符，也不得使用 [Unicode 字符表](https://www.utf8-chartable.de/)中 U+00A1 以上的任何其他 UTF-8 字符|
| 备注 | URI 也会被拒绝，因为没有特定的方法可以给 URI 发音|

文本在导入时，会进行文本规范化，使之可供系统处理。 但是，用户在上传数据之前，必须完成一些重要的规范化操作。 请参阅 [Transcription guidelines](prepare-transcription.md)（听录准则），确定在准备语言数据时哪一种语言合适。

## <a name="language-support"></a>语言支持

查看自定义**语音转文本**语言模型的完整[受支持语言](language-support.md#text-to-speech)列表。



## <a name="import-the-language-data-set"></a>导入语言数据集

选择“语言数据集”行中的“导入”按钮，此时站点会显示一个用于上传新数据集的页面。

准备导入语言数据集时，请登录到[语音服务门户](https://customspeech.ai)。 首先，在顶部功能区中选择“自定义语音”下拉菜单。 然后选择“适应数据”。 首次将数据上传到语音服务时，会看到名为“数据集”的空表。

若要导入新的数据集，请选择“语言数据集”行中的“导入”按钮。 随后站点会显示一个用于上传新数据集的页面。 输入用于标识未来的数据集的“名称”和“说明”，然后选择区域设置。

接下来，使用“选择文件”按钮找到语言数据文本文件。 然后，选择“导入”以便上传数据集。 导入过程可能需要数分钟的时间，具体取决于数据集的大小。

![尝试](media/stt/speech-language-datasets-import.png)

导入完成后，语言数据包含一个对应于语言数据集的条目。 请注意，为该条目分配了一个唯一 ID (GUID)。 此数据还会有一个反映其当前状态的状态。 如果正在排队等待处理，则其状态为“正在等待”；如果正在进行验证，则其状态为“正在处理”；如果数据已做好使用准备，则其状态为“完成”。 数据验证将对文件中的文本执行一系列检查。 它还会对数据执行某种文本规范化。

当状态为“完成”时，可以选择“查看报告”以查看语言数据验证报告。 将会显示通过验证和未通过验证的陈述的数目，以及未通过验证的陈述的详细信息。 在以下示例中，两个示例因字符不正确而未通过验证。 （在此数据集中，第一行包含两个制表符，第二行的多个字符不在 ASCII 可打印字符集中，第三行是空白的）。

![尝试](media/stt/speech-language-datasets-report.png)

当语言数据集的状态为“完成”后，即可将它用来创建自定义语言模型。

![尝试](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>创建自定义语言模型

语言数据准备好以后，请选择“菜单”下拉菜单中的“语言模型”，启动创建自定义语言模型的过程。 此页包含一个名为“语言模型”的表，其中有你当前的自定义语言模型。 如果尚未创建任何自定义语言模型，则此表将为空。 当前区域设置显示在相关数据条目旁边的表中。

在采取任何措施之前，必须选择相应的区域设置。 当前区域设置在所有数据、模型和部署页面的表标题中指示。 若要更改区域设置，请选择位于表标题下的“更改区域设置”按钮。  随后会转到区域设置确认页。 选择“确定”返回到表。

在“创建语言模型”页上输入“名称”和“说明”，以便跟踪此模型的相关信息，例如所使用的数据集。 接下来，从下拉菜单中选择“基础语言模型”。 可以从此模型着手进行自定义。

有两个基础语言模型可供选择。 搜索和听写模型适用于在应用程序中发出的语音，例如命令、搜索查询或听写。 “聊天”模型适用于识别以聊天形式说出的语音。 此类语音通常是面对另一个人在呼叫中心或会议中发出。 名为“通用”的新模型也已推出正式版。 “通用”模型旨在满足所有方案，最终会取代“搜索”、“听写”和“对话”模型。

如以下示例中所示，在指定基础语言模型后，请使用“语言数据”下拉菜单选择要用于自定义的语言数据集。

![尝试](media/stt/speech-language-models-create2.png)

就声学模型的创建来说，可以选择在处理完成后对新的模型执行离线测试。 模型评估需要声学数据集。

若要对语言模型执行离线测试，请选中“离线测试”旁边的复选框。 然后，从下拉菜单中选择一个声学模型。 如果尚未创建任何自定义声学模型，Microsoft 基础声学模型将是菜单中的唯一模型。 如果已选取对话 LM 基础模型，则需在此处使用对话 AM。 如果使用搜索和听写 LM 模型，则需选择搜索和听写 AM 模型。

最后，请选择要用于执行评估的声学数据集。

若已做好开始处理的准备，请选择“创建”。 接下来会看到语言模型表。 表中会有一个对应于此模型的新条目。 状态反映模型的状态。将会经历多个状态，包括“正在等待”、“正在处理”和“完成”。

当模型处于“完成”状态后，可将其部署到终结点。 选择“查看结果”会显示离线测试（如果已执行）的结果。

若要更改模型的“名称”或“说明”，可以使用语言模型表的相应行中的“编辑”链接进行更改。

## <a name="next-steps"></a>后续步骤

- [获取语音服务试用订阅](https://www.azure.cn/zh-cn/home/features/cognitive-services/)
- [如何在 C# 应用中识别语音](quickstart-csharp-dotnet-windows.md)
- [Git 示例数据](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
