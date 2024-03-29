---
title: 发布区域和终结点
titleSuffix: Azure Cognitive Services
description: 3 个创作区域及其门户支持所有多个发布区域。 发布 LUIS 应用的区域对应于创建 Azure LUIS 终结点密钥时在 Azure 门户中指定的区域或位置。 发布应用时，LUIS 会自动为与密钥关联的区域生成终结点 URL。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 0677214adb278bb7773a2e10ad019803a997ff6b
ms.sourcegitcommit: e77582e79df32272e64c6765fdb3613241671c20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135851"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>创作和发布区域及关联的密钥

相应的 LUIS 门户支持三个创作区域。 若要将 LUIS 应用发布到多个区域，每个区域至少需要一个密钥。 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS 创作区域
基于区域，有三个 LUIS 创作门户。 必须在同一区域中创建和发布应用。 

|LUIS|创作区域|Azure 区域名称|
|--|--|--|
|[luis.azure.cn][luis.azure.cn]|中国| `chinaeast`|

创作区域具有[配对故障转移区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

<a name="regions-and-azure-resources"></a>

## 发布区域和 Azure 资源 <a name="publishing-regions"></a>
该应用将发布到与 LUIS 门户中添加的 LUIS 资源关联的所有区域。 例如，对于在 [luis.azure.cn][luis.azure.cn] 上创建的应用，如果你在 **chinaeast** 中创建 LUIS 或认知服务资源并[将其作为资源添加到该应用](luis-how-to-azure-subscription.md)，则该应用将发布到此区域中。 

## <a name="public-apps"></a>公共应用
公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与其资源密钥关联的任何区域中访问该应用。

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>发布区域绑定到创作区域

创作区域中的应用仅可发布到对应的发布区域。 如果应用目前位于错误的创作区域中，请导出应用，然后将其导入发布区域对应的正确创作区域。 


## <a name="publishing-to-china"></a>发布到中国 

若要发布到中国区域，请仅在 https://luis.azure.cn 创建 LUIS 应用。 如果尝试使用中国区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://luis.azure.cn 。 在 [https://luis.azure.cn][luis.azure.cn] 中创建的 LUIS 应用不会自动迁移到其他区域。 要实现迁移，请导出然后再导入 LUIS 应用。

## 中国发布区域 <a name="publishing-to-china"></a>

 全球区域 | 创作 API 区域和创作网站| 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| [中国](#publishing-to-china) | `chinaeast`<br>[luis.azure.cn][luis.azure.cn]| 中国东部<br>`chinaeast`     |  https://chinaeast2.api.cognitive.azure.cn/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>发布到其他区域

若要发布到其他区域，请仅在 [https://luis.azure.cn](https://luis.azure.cn) 创建 LUIS 应用。 

## <a name="other-publishing-regions"></a>其他发布区域

 全球区域 | 创作 API 区域和创作网站| 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| 亚洲 | `chinaeast`<br>[luis.azure.cn][luis.azure.cn]| 中国东部 2<br>`centralindia` |  https://centralindia.api.cognitive.azure.cn/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |


## <a name="endpoints"></a>终结点

LUIS 当前具有 2 个终结点：一个用于创作，一个用于查询预测分析。

|目的|URL|
|--|--|
|创作|`https://{region}.api.cognitive.azure.cn/luis/api/v2.0/apps/{appID}/`|
|文本分析（查询预测）|`https://{region}.api.cognitive.azure.cn/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

下表说明了上表中用大括号 `{}` 表示的参数。

|参数|目的|
|--|--|
|region|Azure 区域 - 创作和发布具有不同的区域|
|appID|在 URL 路由中使用的 LUIS 应用 ID，可在应用仪表板上找到|
|q|从客户端应用程序（如聊天机器人）发送的话语文本|

## <a name="failover-regions"></a>故障转移区域

每个区域都有一个要故障转移到的次要区域。 中国在中国内部故障转移，中国在中国内部故障转移。

创作区域具有[配对故障转移区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预生成实体参考](./luis-reference-prebuilt-entities.md)

 [luis.azure.cn]: https://luis.azure.cn
 [luis.azure.cn]: https://luis.azure.cn




