---
title: 从自定义语音服务迁移到语音服务
titlesuffix: Azure Cognitive Services
description: 自定义语音服务现在是语音服务的一部分。 切换到语音服务可受益于最新的质量和功能更新。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 10/01/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: c0af3a8e3004627e8a3103f58d3650cf289ae472
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348376"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>从自定义语音服务迁移到语音服务

本文介绍如何将应用程序从自定义语音服务迁移到语音服务。

自定义语音服务现在是语音服务的一部分。 切换到语音服务可受益于最新的质量和功能更新。

## <a name="migration-for-new-customers"></a>新客户的迁移

使用语音服务的按小时定价模型更简单。  

1. 在应用程序的每个可用区域中创建一个 Azure 资源。 Azure 资源名称为 **Speech**。 可对同一区域中的以下服务使用单个 Azure 资源，而无需创建不同的资源：

    * 语音转文本
    * 自定义语音转文本
    * 文本转语音
    * 语音翻译

2. 下载[语音 SDK](speech-sdk.md)。

3. 遵照快速入门指南和 SDK 示例使用正确的 API。 如果使用 REST API，则还需要使用正确的终结点和资源密钥。

4. 将客户端应用程序更新为使用语音服务和 API。

## <a name="migration-for-existing-customers"></a>现有客户的迁移

将现有资源密钥迁移到语音服务门户上的语音服务。 使用以下步骤：

> [!NOTE]
> 只能迁移同一区域中的资源密钥。

1. 登录到 [cris.ai](https://www.cris.ai) 门户，然后在右上角菜单中选择订阅。

2. 选择“迁移选定的订阅”。

3. 在文本框中输入订阅密钥，然后选择“迁移”。

## <a name="next-steps"></a>后续步骤

* [试用语音服务](get-started.md)。
* 了解[语音转文本](./speech-to-text.md)的概念。

## <a name="see-also"></a>另请参阅

* [什么是语音服务](overview.md)
* [语音服务和 SDK 文档](speech-sdk.md#get-the-sdk)
