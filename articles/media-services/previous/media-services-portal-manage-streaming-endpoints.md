---
title: 使用 Azure 门户管理流式处理终结点 | Microsoft Docs
description: 本主题说明如何使用 Azure 门户管理流式处理终结点。
services: media-services
documentationcenter: ''
author: WenJason
writer: juliako
manager: digimobile
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: f0c3bbbf7a5c84d069b275389e6fe9a4b908cf0c
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624108"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>使用 Azure 门户管理流式处理终结点 

本文说明了如何使用 Azure 门户管理流式处理终结点。 

>[!NOTE]
>请确保查看[概述](media-services-streaming-endpoints-overview.md)一文。 

有关如何缩放流式处理终结点的信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)文章。

## <a name="start-managing-streaming-endpoints"></a>开始管理流式处理终结点 

要开始管理帐户的流式处理终结点，请执行以下操作。

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择 Azure 媒体服务帐户。
2. 在“设置”边栏选项卡中，选择“流式处理终结点”。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> 仅当流式处理终结点处于运行状态时才进行计费。

## <a name="adddelete-a-streaming-endpoint"></a>添加/删除流式处理终结点

>[!NOTE]
>无法删除默认流式处理终结点。

若要使用 Azure 门户添加/删除流式处理终结点，请执行以下操作：

1. 若要添加流式处理终结点，请单击页面顶部的“+ 终结点”  。 

    如果计划使用不同的 CDN 或者使用一个 CDN 和直接访问，则可能需要多个流式处理终结点。

2. 若要删除流式处理终结点，请按“删除”  按钮。      
3. 单击“启动”  按钮以启动流式处理终结点。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>配置流式处理终结点
通过流式处理终结点可以配置以下属性：

* 访问控制
* 缓存控制
* 跨网站访问策略

有关这些属性的详细信息，请参阅 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)。

可通过执行以下操作配置流式处理终结点：

1. 选择要配置的流式处理终结点。
2. 单击“设置”。

后面提供了字段的简要说明。

![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 最大缓存策略：用于为资产配置通过此流式处理终结点提供的缓存生存期。 如果未设置任何值，则使用默认值。 也可直接在 Azure 存储中定义默认值。 如果为流式处理终结点启用了 Azure CDN，则不应设置小于 600 秒的缓存策略值。  
2. 允许的 IP 地址：用于指定允许连接到发布的流式处理终结点的 IP 地址。 如果未指定 IP 地址，则任何 IP 地址都可以连接。 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.255.0)”）。
3. Akamai 签名标头身份验证的配置：用于指定如何配置 Akamai 服务器发出的签名标头身份验证请求。 到期日期以 UTC 时间表示。

## <a name="scale-your-premium-streaming-endpoint"></a>缩放高级流式处理终结点

有关详细信息，请参阅[本文](media-services-portal-scale-streaming-endpoints.md)。
<!--Update_Description: wording update-->
