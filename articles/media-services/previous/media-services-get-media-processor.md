---
title: 如何使用用于 .NET 的 Azure 媒体服务 SDK 创建媒体处理器 | Microsoft Docs
description: 了解如何创建一个媒体处理器组件来为 Azure 媒体服务编码、转换格式、加密或解密媒体内容。 代码示例用 C# 编写且使用适用于 .NET 的媒体服务 SDK。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/30/2018
ms.date: 12/03/2018
ms.author: juliako
ms.openlocfilehash: 9f7b7c4990dbf91bf3e760535a063894093823ae
ms.sourcegitcommit: bfd0b25b0c51050e51531fedb4fca8c023b1bf5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52673143"
---
# <a name="how-to-get-a-media-processor-instance"></a>如何：获取媒体处理器实例
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概述
在媒体服务中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。 通常，创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

## <a name="azure-media-processors"></a>Azure 媒体处理器 

以下主题提供媒体处理器列表：

* [编码媒体处理器](scenarios-and-availability.md#encoding-media-processors)
* [分析媒体处理器](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>获取媒体处理器

以下方法演示了如何获取媒体处理器实例。 该代码示例假设使用名为“_context”的模块级变量来引用 [如何：以编程方式连接到媒体服务](media-services-use-aad-auth-to-access-ams-api.md)部分中描述的服务器上下文。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="next-steps"></a>后续步骤
了解如何获取媒体处理器实例后，请转到[如何对资产进行编码](media-services-dotnet-encode-with-media-encoder-standard.md)主题，其中说明了如何使用 Media Encoder Standard 对资产进行编码。
<!--Update_Description: udpate two links-->
