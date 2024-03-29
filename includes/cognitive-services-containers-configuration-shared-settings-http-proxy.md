---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 05/07/2019
ms.date: 06/11/2019
ms.openlocfilehash: f4749e091a850485a15dafa1ddc0ee31242ce8ca
ms.sourcegitcommit: 259c97c9322da7add9de9f955eac275d743c9424
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66830154"
---
如果需要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

| Name | 数据类型 | 说明 |
|--|--|--|
|HTTP_PROXY|字符串|要使用的代理，例如 `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|字符串|对代理进行身份验证所需的凭据，例如 username:password。|
|`<proxy-user>`|字符串|代理的用户。|
|`proxy-password`|字符串|与代理的 `<proxy-user>` 关联的密码。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```

