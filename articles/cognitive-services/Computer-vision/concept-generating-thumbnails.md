---
title: 生成缩略图 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 生成图像的缩略图的相关概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
origin.date: 08/29/2018
ms.date: 10/30/2018
ms.author: v-junlch
ms.openlocfilehash: 13efe74c19416e076bc72078e5cfe5ddbac5e550
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52666865"
---
# <a name="generating-thumbnails"></a>生成缩略图

缩略图是完整大小图像的小型呈现。 设备不同（例如手机、平板电脑和电脑），需要的用户体验 (UX) 布局和缩略图大小也不同。 使用智能裁剪，此计算机视觉功能可帮助解决该问题。

上传图像后，计算机视觉会生成高质量缩略图，然后通过分析图像中的对象来识别感兴趣区域 (ROI)。 它可以选择性地裁剪图像以满足 ROI 的要求。 可以使用与原始图像的纵横比不同的纵横比显示生成的缩略图以满足需求。

缩略图算法的原理如下所示：

1. 从图像中删除让人分散注意力的元素并识别主对象，即感兴趣区域。
2. 根据标识的感兴趣区域裁剪图像。
3. 更改纵横比，使之适合目标缩略图维度。

生成的缩略图可能会根据指定的高度、宽度和智能裁剪的不同而有很大差异，如下图所示。

![缩略图](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>缩略图生成示例

下表说明了计算机视觉为示例图像生成的典型缩略图。 生成缩略图的指定目标高度和宽度为 50 像素，并且启用了智能裁剪。

| 映像 | 缩略图 |
|-------|-----------|
|![户外山地](./Images/mountain_vista.png) | ![户外山脉缩略图](./Images/mountain_vista_thumbnail.png) |
|![视觉分析花](./Images/flower.png) | ![视觉分析花缩略图](./Images/flower_thumbnail.png) |
|![屋顶的女人](./Images/woman_roof.png) | ![女士屋顶缩略图](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)的概念。
