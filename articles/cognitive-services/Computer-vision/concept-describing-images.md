---
title: 描述图像 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 描述图像的相关概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
origin.date: 08/29/2018
ms.date: 10/30/2018
ms.author: v-junlch
ms.openlocfilehash: 9c83c8ca84c506d87a55e23acce764387727f580
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52653687"
---
# <a name="describing-images"></a>描述图像

计算机视觉的算法可分析图像中的内容。 此分析形成“说明”的基础。该“说明”以人类可读语言显示，采用完整句子。 说明汇总了图像中找到的内容。 计算机视觉的算法可根据图像中标识的视觉特征生成各种说明。 每项说明都经过评估并带有置信度分数。 然后返回一个列表，将置信度从高到低进行排列。

## <a name="image-description-example"></a>图像说明示例

以下 JSON 响应表明计算机视觉在基于视觉特征对示例图像进行描述时返回的内容。

![黑白建筑](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)的概念。
