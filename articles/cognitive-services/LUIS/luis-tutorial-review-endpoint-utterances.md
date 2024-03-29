---
title: 审核终结点陈述
titleSuffix: Azure Cognitive Services
description: 通过验证或更正 LUIS 不确定的、通过 LUIS HTTP 终结点收到的陈述，改进应用预测。 某些陈述可能需要针对意向进行验证，而另一些陈述可能需要针对实体进行验证。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: e8166baca9ca79882aac42d1ea39c778ae2ade39
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557079"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>教程：通过查看终结点话语来修复不确定的预测
在本教程中，你将通过验证或更正 LUIS 不确定的、通过 LUIS HTTPS 终结点收到的陈述，改进应用预测。 某些陈述可能需要针对意向进行验证，而另一些陈述可能需要针对实体进行验证。 你应当在计划的 LUIS 维护中定期评审终结点陈述。 

此审核过程是 LUIS 了解应用领域的另一种方式。 LUIS 已选择了审核列表中显示的陈述。 此列表具有以下特点：

* 特定于应用。
* 旨在改进应用的预测准确性。 
* 应该定期进行审核。 

可以通过审核终结点表述来验证或纠正表述的已预测意向。 另外，请标记未预测的或未正确预测的自定义实体。 

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入示例应用
> * 查看终结点话语
> * 更新短语列表
> * 训练应用
> * 发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>导入示例应用

继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

使用以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json)。

1. 将 JSON 导入到新应用中。

1. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `review`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

1. 训练并发布新应用。

1. 使用终结点添加以下言语。 可以使用[脚本](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js)或从浏览器的终结点中执行此操作。 要添加的表述如下：

```
  /* all utterances up through the Sentiment tutorial in series */
  const endpointUtterances = [
      "I'm looking for a job with Natural Language Processing",
      "I want to cancel on March 3",
      "When were HRF-123456 and hrf-234567 published in the last year?",
      "shift 123-45-6789 from Z-1242 to T-54672",
      "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "Here is my c.v. for the programmer job",
      "This is the lead welder paperwork.",
      "does form hrf-123456 cover the new dental benefits and medical plan",
      "Jill Jones work with the media team on the public portal was amazing",
  ];
```

    If you have all the versions of the app, through the series of tutorials, you may be surprised to see that the **Review endpoint utterances** list doesn't change, based on the version. There is a single pool of utterances to review, regardless of which version you are actively editing or which version of the app was published at the endpoint. 

## <a name="review-endpoint-utterances"></a>查看终结点话语

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 从左侧导航栏中选择“审核终结点表述”。 列表会筛选出 **ApplyForJob** 意向。 

    [![左侧导航栏中“审核终结点表述”按钮的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. 切换“实体”视图，查看标记的实体。 
    
    [![“审核终结点表述”的屏幕截图，突出显示了“实体”视图切换](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |话语|正确的意向|缺失的实体|
    |:--|:--|:--|
    |我要找一份使用自然语言处理的工作|GetJobInfo|工作 -“自然语言处理”|

    此表述的意向不正确，其分数低于 50%。 **ApplyForJob** 意向有 21 个表述，而 **GetJobInformation** 意向则有 7 个表述。 除了应正确匹配终结点表述，还应向 **GetJobInformation** 意向添加更多的表述。 这是一项练习，需要你自行完成。 每个意向（**None** 意向除外）的示例表述数目应该基本相同。 **None** 意向应包含应用中总表述数的 10%。 

1. 对于表述 `I'm looking for a job with Natual Language Processing`，请在“匹配的意向”列中选择正确的意向 **GetJobInformation**。 

    [![“审核终结点表述”的屏幕截图，将表述与意向进行了匹配](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

1. 在同一表述中，`Natural Language Processing` 的实体为 keyPhrase。 这本应是“工作”实体。 选择 `Natural Language Processing`，然后从列表选择“工作”实体。

    [![“审核终结点表述”的屏幕截图，已在表述中对实体进行了标记](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

1. 在同一行的“添加到匹配的意向”列中选择画圈的复选标记。 

    [![在意向中确定表述匹配项的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    此操作将表述从“审核终结点表述”移到 **GetJobInformation** 意向。 此终结点表述现在是该意向的一个示例表述。 

1. 审核此意向中的剩余表述，标记不正确的表述并纠正“匹配的意向”。

1. 如果所有表述都正确，请选择每一行的复选框，然后选择“添加所选项”，以便正确匹配这些表述。 

    [![确定剩余表述与匹配的意向的关系的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

1. 此列表应该再也不会有这些表述。 如果出现更多的表述，请继续完成列表中的项目，纠正意向并标记缺失的实体，直至此列表为空。 

1. 选择“筛选器”列表中的下一意向，然后继续纠正表述并对实体进行标记。 请记住，每个意向的最后一步是选择表述行中的“添加到匹配的意向”，或者勾选每个意向的框，然后选择表上面的“添加所选项”。

    继续操作，直到筛选器列表中的所有意向和实体都具有空列表。 这是一个很小的应用。 审核过程只需数分钟。 

## <a name="update-phrase-list"></a>更新短语列表
不断向短语列表添加新发现的工作名称。 

1. 从左侧导航栏中选择“短语列表”。

2. 选择“工作”短语列表。

3. 将 `Natural Language Processing` 作为值添加，然后选择“保存”。 

## <a name="train"></a>定型

在训练之前，LUIS 并不知道这些更改。 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

如果已导入此应用，则需选择“情绪分析”。

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

尝试一个与纠正的表述相近的表述。 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Are there any natural language processing jobs in my department right now?`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   通过一个高分预测到了正确意向，检测到的“工作”实体为 `natural language processing`。 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>是否可以通过添加更多的表述来代替审核？ 
你可能会想，为何不添加更多的示例表述呢？ 审核终结点表述的目的是什么？ 在实际的 LUIS 应用中，终结点表述来自各个用户，其遣词造句方式与你并不相同。 如果同样的词汇和句式多次使用，则原始预测的百分比会更高。 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>为何顶级意向会包含在表述列表中？ 
某些终结点表述在审核列表中的预测分数会很高。 仍需审核并验证这些表述。 这些顶级意向之所以在列表中，是因为次高分数意向的分数与顶级意向的分数很接近。 你希望两个顶级意向之间存在大约 15% 的差异。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
在本教程中，你已审核了在终结点提交的、LUIS 不确定的表述。 在验证这些表述并将其作为示例表述移动到正确的意向中后，LUIS 将可以提高预测准确度。

> [!div class="nextstepaction"]
> [了解如何使用模式](luis-tutorial-pattern.md)




