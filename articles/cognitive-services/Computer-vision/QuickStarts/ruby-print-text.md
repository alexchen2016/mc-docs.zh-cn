---
title: 快速入门：提取印刷体文本 - REST、Ruby
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 Ruby 从图像中提取印刷体文本。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
origin.date: 03/11/2019
ms.date: 05/14/2019
ms.author: v-junlch
ms.custom: seodec18
ms.openlocfilehash: 76baaed498b3b0a1ef5a62f798e63f53ea732cb2
ms.sourcegitcommit: 9235a1f313393f21b5c42cb7a1626b1b93feb8be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65598878"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-ruby-in-computer-vision"></a>快速入门：使用计算机视觉中的 REST API 和 Ruby 提取印刷体文本 (OCR)

在本快速入门中，你将使用计算机视觉的 REST API，通过光学字符识别 (OCR) 从图像中提取印刷体文本。 借助 [OCR](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 方法，可检测图像中的印刷体文本，并将识别的字符提取到计算机可用的字符流中。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [Ruby](https://www.ruby-lang.org/en/downloads/) 2.4.x 或更高版本。
- 必须具有计算机视觉的订阅密钥。 你可以按照[创建认知服务帐户](/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `<Subscription Key>` 替换为订阅密钥。
    1. 必要时，请将 `https://api.cognitive.azure.cn/vision/v2.0/ocr` 替换为向你提供了订阅密钥的 Azure 区域中的 [OCR](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 方法的终结点 URL。
    1. （可选）将 `https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\` 替换为要从中提取印刷体文本的另一图像的 URL。
1. 将代码保存为以 `.rb` 为扩展名的文件。 例如，`get-printed-text.rb`。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `ruby` 命令运行示例。 例如，`ruby get-printed-text.rb`。

```ruby
require 'net/http'

uri = URI('https://api.cognitive.azure.cn/vision/v2.0/ocr')
uri.query = URI.encode_www_form({
    # Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "language": "en",
  "textAngle": -2.0000000000000338,
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "462,379,497,258",
      "lines": [
        {
          "boundingBox": "462,379,497,74",
          "words": [
            {
              "boundingBox": "462,379,41,73",
              "text": "A"
            },
            {
              "boundingBox": "523,379,153,73",
              "text": "GOAL"
            },
            {
              "boundingBox": "694,379,265,74",
              "text": "WITHOUT"
            }
          ]
        },
        {
          "boundingBox": "565,471,289,74",
          "words": [
            {
              "boundingBox": "565,471,41,73",
              "text": "A"
            },
            {
              "boundingBox": "626,471,150,73",
              "text": "PLAN"
            },
            {
              "boundingBox": "801,472,53,73",
              "text": "IS"
            }
          ]
        },
        {
          "boundingBox": "519,563,375,74",
          "words": [
            {
              "boundingBox": "519,563,149,74",
              "text": "JUST"
            },
            {
              "boundingBox": "683,564,41,72",
              "text": "A"
            },
            {
              "boundingBox": "741,564,153,73",
              "text": "WISH"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44)

