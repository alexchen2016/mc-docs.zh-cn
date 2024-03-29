---
title: 使用 Azure 门户缩放媒体处理 | Microsoft Docs
description: 本教程逐步介绍了如何使用 Azure 门户缩放媒体处理。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/05/2018
ms.date: 12/03/2018
ms.author: v-jay
ms.openlocfilehash: 267fdd3db16b1f76665efdd0ad593d1adfa9ef1d
ms.sourcegitcommit: bfd0b25b0c51050e51531fedb4fca8c023b1bf5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52672630"
---
# <a name="change-the-reserved-unit-type"></a>更改预留单位类型
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [门户](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概述

媒体服务帐户与预留单位类型关联，后者决定了编码处理任务的处理速度。 可以在以下预留单位类型中进行选择：**S1**、**S2** 或 **S3**。 例如，与 **S1** 预留单位类型相比，使用 **S2** 预留单位类型时，同一编码作业运行速度更快。

除了指定预留单位类型，还可以指定为帐户预配预留单位 (RU)。 预配的 RU 数决定了给定帐户中可并发处理的媒体任务数。

>[!NOTE]
>RU 可用于并行化所有媒体处理，包括使用 Azure Media Indexer 为作业编制索引。 但是，与编码不同，索引作业使用更快的预留单位并不能更快地完成处理。

> [!IMPORTANT]
> 请确保查看[概述](media-services-scale-media-processing-overview.md)主题，以获取有关缩放媒体处理主题的详细信息。
> 
> 

## <a name="scale-media-processing"></a>调整媒体处理的规模
若要更改预留单位类型和预留单位数目，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“媒体预留单位”。
   
    若要更改所选预留单位类型的预留单位数，请使用屏幕顶部的“媒体预留单位”滑块。
   
    若要更改“预留单位类型”，请单击“预留处理单位的速度”栏。 然后，选择所需的定价层：S1、S2 或 S3。
   
3. 按“保存”按钮保存更改。
   
    按“保存”后，会立即分配新的预留单位。

<!--Update_Description:new file-->