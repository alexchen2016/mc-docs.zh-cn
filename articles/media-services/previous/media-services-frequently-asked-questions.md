---
title: Azure 媒体服务常见问题 | Microsoft 文档
description: 常见问题 (FAQ)
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/10/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: a07a6826b6b7122ef5a64e40733a5a6ef3a0bb7f
ms.sourcegitcommit: 7b93bc945ba49490ea392476a8e9ba1a273098e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833353"
---
# <a name="frequently-asked-questions"></a>常见问题

本文介绍由 Azure 媒体服务 (AMS) 用户社区提出的常见问题。

## <a name="general-ams-faqs"></a>一般性的 AMS 常见问题

问：如何流式传输到 Apple iOS 设备？

答：向 URL 的“/Manifest”部分添加“(format=m3u8-aapl)”路径，告知流式处理源服务器返回供 Apple iOS 本机设备使用的 HLS 内容（有关详细信息，请参阅[传送内容](media-services-deliver-content-overview.md)）。

问：如何缩放索引？

答：编码任务和索引任务的预留单位相同。 请遵循[如何缩放编码预留单位](media-services-scale-media-processing-overview.md)中的说明。 **请注意**，预留单位类型不影响索引器性能。

问：我已经上传、编码并发布了视频。 为什么在尝试对视频进行流式处理时，它不播放？

答：最常见的原因之一是，没有“正在运行”状态下从其播放的流式处理终结点。  

问：我是否可以在实时流上进行合成操作？

答：Azure 媒体服务当前不提供实时流上的合成操作，因此需要在计算机上进行预合成。

问：我是否可以将 Azure CDN 与实时流式处理一起使用？

答：媒体服务支持与 Azure CDN 集成（有关详细信息，请参阅[如何在媒体服务帐户中管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)）。  可以将实时传送视频流与 CDN 结合使用。 Azure 媒体服务提供平滑流式处理、HLS 和 MPEG-DASH 输出。 所有这些格式均使用 HTTP 来传输数据并从 HTTP 缓存中获益。 实时流式处理中，实际视频/音频数据被分为数个片段，并且单个片段缓存于 CDN 中。 唯一需要刷新的数据是清单数据。 CDN 定期刷新清单数据。

问：Azure 媒体服务是否支持存储图像？

答：如果需要存储 JPEG 或 PNG 图像，应将其存储在 Azure Blob 存储中。 除非想要将图像与视频或音频资产相关联，否则将图像放入媒体服务帐户毫无益处。 如果需要在视频编码器中将图像作为叠加层使用，Media Encoder Standard 支持在视频上叠加图像，且它将 JPEG 和 PNG 列为支持的输入格式。 有关详细信息，请参阅[创建覆盖](media-services-advanced-encoding-with-mes.md#overlay)。

问：如何将资产从一个媒体服务帐户复制到另一个媒体服务帐户？

答：要使用 .NET 将资产从一个媒体服务帐户复制到另一个帐户，可以使用 [Azure 媒体服务 .NET SDK 扩展](https://github.com/Azure/azure-sdk-for-media-services-extensions/)存储库中提供的 [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 扩展方法。 有关详细信息，请参阅 [此](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) 论坛主题。

问：AMS 支持使用哪些字符来为文件命名？

答：构建流内容的 URL 时，媒体服务会使用 IAssetFile.Name 属性的值（如 http://{AMSAccount}.origin.mediaservices.chinacloudapi.cn/{GUID}/{IAssetFile.Name}/streamingParameters。）出于这个原因，不允许使用百分号编码。 **Name** 属性的值不能含有任何以下[百分号编码保留字符](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)：!*'();:@&=+$,/?%#[]"。 此外，只能有一个“.” 此外，文件扩展名中只能含有一个“.”。

问：如何使用 REST 进行连接？

答：若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

问：如何在编码过程中旋转视频？

答：[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) 支持旋转 90/180/270 度。 默认行为是“自动”，即尝试在传入的 MP4/MOV 文件中检测旋转元数据并对其进行补偿。 包含 **此处** 定义的 json 预设之一的以下 [Sources](media-services-mes-presets-overview.md)元素：

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


<!--Update_Description: add Q/A for "How do you stream to Apple iOS devices"-->