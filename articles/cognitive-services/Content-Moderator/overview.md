---
title: 什么是 Azure 内容审查器？
titlesuffix: Azure Cognitive Services
description: 了解如何使用内容审查器跟踪、标记、评估和筛选用户生成的内容中不适合的材料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
origin.date: 02/20/2019
ms.date: 06/10/2019
ms.author: v-junlch
ms.openlocfilehash: cdf7ecc2632848f327eda20584807d8c3bc388cf
ms.sourcegitcommit: 259c97c9322da7add9de9f955eac275d743c9424
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66830118"
---
# <a name="what-is-azure-content-moderator"></a>什么是 Azure 内容审查器？

Azure 内容审查器 API 是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、危险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。 请参阅[审查 API](#moderation-apis) 部分，详细了解不同内容标记表示的意思。

## <a name="where-it-is-used"></a>使用位置

下面是软件开发人员或团队会使用内容审察器的一些场景：

- 在联机市场中审查产品目录和其他用户生成的内容
- 在游戏公司中审查用户生成的游戏项目和聊天室
- 在社交通讯平台中审查用户添加的图像、文本和视频
- 企业媒体公司对其内容进行集中式审查
- K-12 教育解决方案提供商为学生和教师筛选掉不当的内容

## <a name="what-it-includes"></a>组成部分

内容审查器服务包含多个可以通过 REST 调用和 .NET SDK 使用的 Web 服务 API。 

## <a name="moderation-apis"></a>审查 API

内容审查器服务包括了审查 API，这些 API 检查内容中是否存在可能不适当或令人反感的材料。

![内容审查器审查 API 的框图](images/content-moderator-mod-api.png)

下表介绍了各种类型的审查 API。

| API 组 | 说明 |
| ------ | ----------- |
|[**文本审查**](text-moderation-api.md)| 扫描文本中是否存在冒犯性内容、明确的或暗示性的色情内容、不雅内容和个人数据。|
|[**自定义术语列表**](try-terms-list-api.md)| 扫描文本中是否存在内置的术语和一系列的自定义术语。 根据你自己的内容策略，使用自定义列表阻止或允许内容。|  
|[**图像审查**](image-moderation-api.md)| 扫描图像中是否存在成人内容或不雅内容，通过光学字符识别 (OCR) 功能检测图像中的文本，以及检测人脸。|
|[**自定义图像列表**](try-image-list-api.md)| 针对自定义图像列表对图像进行扫描。 使用自定义图像列表筛选掉常常反复出现的内容的实例，这些实例不需再次分类。|
|[**视频审查**](video-moderation-api.md)| 扫描视频中是否存在成人内容或挑逗性内容，并针对上述内容返回时间标记。|

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用内容审查器服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.trustcenter.cn)来了解详细信息。

<!-- Update_Description: wording update -->