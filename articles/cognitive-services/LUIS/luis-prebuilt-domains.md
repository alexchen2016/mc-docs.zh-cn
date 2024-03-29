---
title: 用于语言理解的预生成域
titleSuffix: Azure Cognitive Services
description: LUIS 提供了一组预生成域，用以快速添加常用的对话式用户方案。
services: cognitive-services
author: lingliw
ms.custom: seodec18
manager: digimobile
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 51a8af14aec1ee6e113b5fabe720033a29fb710a
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555651"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>为常用使用方案添加预生成域 

LUIS 通过预生成域提供了一组预生成意向，用以快速添加常用的意向和话语。 这是一种快速简单地向对话式客户端应用添加功能的方法，不需要为这些功能设计模型。 

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. 在“意向”页面上，从左下角的工具栏中选择“添加预生成域”。 

1. 选择“日历”意向并选择“添加域”按钮。

    ![添加“日历”预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 在左侧导航栏中选择“意向”以查看“日历”意向。 此域中每个意向都具有前缀 `Calendar.`。 与话语一起，此域的以下两个实体也添加到应用：`Calendar.Location` 和 `Calendar.Subject`。 

## <a name="train-and-publish"></a>训练并发布

1. 添加域后，通过在右上角的工具栏中选择“训练”来训练应用。 

1. 在顶部工具栏中，选择“发布”。 发布到“生产”。 

1. 当出现绿色的成功通知后，选择“参阅终结点列表”链接来查看终结点。

1. 选择一个终结点。 将会在新的浏览器选项卡中打开该终结点。 使浏览器选项卡保持打开状态，并继续执行“测试”部分。

## <a name="test"></a>测试

通过为 **q** 参数添加值在终结点上测试新意向：`Schedule a meeting with John Smith in Seattle next week`。

LUIS 返回正确的意向和会议主题：

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [预生成域参考](./luis-reference-prebuilt-domains.md)




