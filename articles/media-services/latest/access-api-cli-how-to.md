---
title: 访问 Azure 媒体服务 API - CLI 2.0 | Azure
description: 按照本操作说明的步骤访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
origin.date: 03/19/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.openlocfilehash: 8a5d56294e0a2341dda10587fa771666d82a2dc2
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52656203"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>使用 CLI 2.0 访问 Azure 媒体服务 API
 
应使用 Azure AD 服务主体身份验证连接到 Azure 媒体服务 API。 应用程序需要请求具有以下参数 Azure AD 令牌：

* Azure AD 租户终结点
* 媒体服务资源 URI
* REST 媒体服务的资源 URI
* Azure AD 应用程序值：客户端 ID 和客户端密码

本文介绍了如何使用 CLI 2.0 创建 Azure AD 应用程序和服务主体，以及获取访问 Azure 媒体服务资源所需的值。

## <a name="prerequisites"></a>先决条件 

按照[本快速入门](create-account-cli-quickstart.md)所述，创建新的 Azure 媒体服务帐户。

## <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](http://portal.azure.cn)。

如果选择在本地安装并使用 CLI，本主题要求使用 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/install-azure-cli)。 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式传输文件](stream-files-dotnet-quickstart.md)

