---
title: 迁移活动 DNS 名称 - Azure 应用服务
description: 了解如何在不停机的情况下，将已分配到实时站点的自定义 DNS 域名迁移到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 06/28/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.openlocfilehash: 0400b40d9b16eb9c202ec2dd25d863233d3ecc6c
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627577"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>将活动 DNS 名称迁移到 Azure 应用服务

本文介绍如何在不停机的情况下，将活动 DNS 名称迁移到 [Azure 应用服务](../app-service/overview.md)。

将实时站点及其 DNS 域名迁移到应用服务时，该 DNS 名称已在提供实时流量。 可以在迁移期间提前将活动 DNS 名称绑定到应用服务应用，从而避免 DNS 解析中的停机时间。

如果你不担心 DNS 解析中的停机时间，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)。

## <a name="prerequisites"></a>先决条件

若要完成本操作说明：

- [确保应用服务应用不在“免费”层中](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>提前绑定域名

提前绑定自定义域时，对 DNS 记录进行任何更改之前，需要完成以下两项操作：

- 验证域所有权
- 为应用启用域名

最终将自定义 DNS 名称从旧站点迁移到应用服务应用时，DNS 解析中将不会有停机时间。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>创建域验证记录

若要验证域所有权，请添加 TXT 记录。 TXT 记录从 awverify.&lt;subdomain> 映射到 &lt;appname>.chinacloudsites.cn。 

你需要的 TXT 记录取决于要迁移的 DNS 记录。 有关示例，请参阅下表（`@` 通常表示根域）：

| DNS 记录示例 | TXT 主机 | TXT 值 |
| - | - | - |
| @（根域） | awverify | _&lt;appname>.chinacloudsites.cn_ |
| www（子域） | awverify.www | _&lt;appname>.chinacloudsites.cn_ |
| \*（通配符） | awverify.\* | _&lt;appname>.chinacloudsites.cn_ |

在 DNS 记录页中，记下要迁移的 DNS 名称的记录类型。 应用服务支持来自 CNAME 和 A 记录的映射。

### <a name="enable-the-domain-for-your-app"></a>启用应用的域

在 [Azure 门户](https://portal.azure.cn)中的应用页的左侧导航窗格中，选择“自定义域”。 

![自定义域菜单](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在“自定义域”页中，选择“添加主机名”旁的 + 图标。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

键入已添加 TXT 记录的完全限定的域名，如 `www.contoso.com`。 对于通配符域（如 \*.contoso.com），可以使用与通配符域匹配的任何 DNS 名称。 

选择“验证”。

“添加主机名”按钮会被激活。 

请确保“主机名记录类型”设置为你想要迁移的 DNS 记录类型。

选择“添加主机名”。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

![已添加 CNAME 记录](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

你的自定义 DNS 名称现已在 Azure 应用中启用。 

## <a name="remap-the-active-dns-name"></a>重新映射活动 DNS 名称

唯一需要做的是重新映射活动 DNS 记录以指向应用服务。 现在，它仍然指向旧站点。

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>复制应用的 IP 地址（仅限 A 记录）

如果你要重新映射 CNAME 记录，请跳过此部分。 

若要重新映射 A 记录，需要应用服务应用的外部 IP 地址，此地址显示在“自定义域”页中。

通过选择右上角的 X 关闭“添加主机名”页。 

在“自定义域”页中，复制应用的 IP 地址。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>更新 DNS 记录

返回到域提供商的 DNS 记录页，选择要重新映射的 DNS 记录。

对于 `contoso.com` 根域示例，重新映射 A 或 CNAME 记录，如下表中的示例所示： 


|       FQDN 示例        | 记录类型 | 主机  |                       值                        |
|---------------------------|-------------|-------|----------------------------------------------------|
|    contoso.com (root)     |      A      |  `@`  | 通过[复制应用的 IP 地址](#info)获得的 IP 地址 |
|   www.contoso.com（子域）   |    CNAME    | `www` |         *&lt;appname>.chinacloudsites.cn*          |
| \*.contoso.com（通配符域） |    CNAME    | *\**  |         *&lt;appname>.chinacloudsites.cn*          |

保存设置。

在 DNS 传播发生后，DNS 查询应立即开始解析应用服务应用。

## <a name="next-steps"></a>后续步骤

了解如何将自定义 SSL 证书绑定到应用服务。

> [!div class="nextstepaction"]
> [将现有的自定义 SSL 证书绑定到 Azure 应用服务](app-service-web-tutorial-custom-ssl.md)
