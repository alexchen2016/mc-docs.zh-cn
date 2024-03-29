---
title: API HTTP 响应代码
titleSuffix: Azure
description: 了解从 LUIS 创作和终结点 API 返回的 HTTP 响应代码
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: dc70f9e2e7fff30ca547a2cf7616e8080929798c
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555550"
---
# <a name="common-api-response-codes-and-their-meaning"></a>常见的 API 响应代码及其含义

[创作](https://aka.ms/luis-authoring-apis)和[终结点](https://aka.ms/luis-endpoint-apis) API 返回 HTTP 响应代码。 响应消息包含特定于某个请求的信息，而 HTTP 响应状态代码是通用的。 

## <a name="common-status-codes"></a>常见状态代码
下表列出了[创作](https://aka.ms/luis-authoring-apis)和[终结点](https://aka.ms/luis-endpoint-apis) API 最常见的一些 HTTP 响应状态代码：

|代码|API|说明|
|:--|--|--|
|400|创作、终结点|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|创作、终结点|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|创作|使用的是终结点订阅密钥，而不是创作密钥|
|401|创作、终结点|密钥无效、格式错误或为空|
|401|创作、终结点| 密钥与区域不匹配|
|401|创作|你不是所有者或协作者|
|401|创作|API 调用的顺序无效|
|403|创作、终结点|超出了每月密钥配额总限制|
|409|终结点|应用程序仍在加载|
|410|终结点|需要重新训练和重新发布应用程序|
|414|终结点|查询超过最大字符限制|
|429|创作、终结点|超出速率限制（请求数/秒）|

## <a name="next-steps"></a>后续步骤

* REST API [创作](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)和[终结点](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)文档




