---
title: 使用 Azure 门户缩放流式处理终结点 | Microsoft Docs
description: 本教程逐步演示如何使用 Azure 门户缩放流式处理终结点。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: a91b3c09dfabf6c67687b073cf2a03939203d9b6
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624105"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>使用 Azure 门户缩放流式处理终结点
## <a name="overview"></a>概述

> [!NOTE]
> 若要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)。
> 
> 

**高级**流式处理终结点适合用于高级工作负荷，同时提供可缩放的专用带宽容量。 默认情况下，使用“高级”流式处理终结点的客户会获得一个流式处理单位 (SU)。 可通过添加 SU 来缩放流式处理终结点。 每个 SU 均为应用程序提供额外的带宽容量。 有关流式处理终结点类型和 CDN 配置的详细信息，请参阅[流式处理终结点概述](media-services-streaming-endpoints-overview.md)主题。
 
本主题介绍了如何缩放流式处理终结点。

有关定价详细信息，请参阅[媒体服务定价详细信息](https://go.microsoft.com/fwlink/?LinkId=275107)。

## <a name="scale-streaming-endpoints"></a>缩放流式处理终结点

若要更改流式处理单位数，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“流式处理终结点”。
3. 单击要缩放的流式处理终结点。 

    > [!NOTE] 
    > 只能缩放**高级**流式处理终结点。

4. 滑动滑块，指定流式处理单位数。

    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

<!--Update_Description:update one link-->