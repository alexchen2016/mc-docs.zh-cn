---
title: 开发视频播放器应用程序
description: 本主题提供可用于开发自己的客户端应用程序（这些应用程序使用媒体服务中的流媒体）的播放器框架和插件的链接。
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: c802095833e9f9decef450f6bdd53ff5f18d61be
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624119"
---
# <a name="develop-video-player-applications"></a>开发视频播放器应用程序
## <a name="overview"></a>概述
Azure 媒体服务提供所需的工具，以便创建适用于大多数平台的丰富、动态的客户端播放器应用程序，这些平台包括：iOS 设备、Android 设备、Windows、Windows Phone、Xbox 和机顶盒。 本主题还提供可用于开发自己的客户端应用程序（这些应用程序使用 Azure 媒体服务中的流媒体）的 SDK 和播放器框架的链接。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。  若要开始对内容进行流式处理并利用动态打包和动态加密功能，必须确保要从其流式获取内容的流式处理终结点处于“正在运行”状态。 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://www.azure.cn/home/features/media-services) 是 Web 视频播放器，用于在各种浏览器和设备中播放 Azure 媒体服务中的媒体内容。 Azure Media Player 采用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。 如果无法在设备或浏览器中提供这些标准，Azure 媒体播放器会采用 Flash 和 Silverlight 作为回退技术。 如果不考虑所使用的播放技术，开发人员将有一个统一的 JavaScript 接口来访问 API。 这使 Azure 媒体服务提供的内容无需其他措施便可在各种设备和浏览中轻松播放。

Azure 媒体服务允许播放使用 DASH、平滑流式处理和 HLS 流式处理格式提供的内容。 Azure 媒体播放器会考虑这些不同的格式并基于平台/浏览器功能自动播放最佳链接。 Microsoft Azure 媒体服务还允许使用 PlayReady 加密或 AES 128 位信封加密对资产进行动态加密。 Azure 媒体播放器在合理配置时允许对 PlayReady 和 AES-128 位加密的内容进行解密。 

更多相关信息：

* [Azure Media Player](https://www.azure.cn/home/features/media-services)
* [Azure 媒体播放器文档](https://aka.ms/ampdocs) 
* [Azure 媒体播放器入门博客](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [注册以保持最新版本的 Azure 媒体播放器](https://aka.ms/ampsignup)
* [添加新的功能请求、想法和反馈](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>用于创建播放器应用程序的其他工具
也可以使用以下任意 SDK：

* [平滑流式处理客户端 SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [平滑流式处理 Windows 应用商店应用](media-services-build-smooth-streaming-apps.md)
* [Microsoft 媒体平台：播放器框架](https://playerframework.codeplex.com/) 
* [HTML5 Player Framework 文档](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming Plugin for OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [授权 Microsoft® 平滑流式处理客户端移植工具包](https://aka.ms/sspk) 
* [XBOX 视频应用程序开发](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>广告
Azure 媒体服务通过“Windows 媒体平台：播放器框架”提供广告插入支持。 附带广告支持的播放器框架在 Windows 8、Silverlight、Windows Phone 8 和 iOS 设备上均可用。 每个播放器框架包含演示如何实现播放器应用程序的示例代码。 可以插入媒体中的广告有三种不同类型：

线性 - 暂停主视频的全帧广告

非线性 - 播放主视频时显示的覆盖式广告，通常为放置在播放器内的一个徽标或其他静态图像

随播 - 在播放器之外显示的广告

广告可置于主视频时间线中的任何一个时间点。 必须告知播放器何时播放广告以及播放哪些广告。 这使用一组标准的基于 XML 的文件来完成：视频广告服务模板 (VAST)、数字视频多广告播放列表 (VMAP)、媒体抽象排序模板 (MAST) 和数字视频播放器广告接口定义 (VPAID)。 VAST 文件用于指定要显示哪些广告。 VMAP 文件用于指定何时播放各种广告并且包含 VAST XML。 MAST 文件是对包含 VAST XML 的广告进行排序的另一种方法。 VPAID 文件用于定义视频播放器与广告或广告服务器之间的接口。 有关详细信息，请参阅 [插入广告](https://msdn.microsoft.com/library/dn387398.aspx)。

有关在实时传送视频流视频中隐藏式字幕和广告支持的信息，请参阅 [支持的隐藏式字幕和广告插入标准](./media-services-manage-channels-overview.md)。

## <a name="see-also"></a>另请参阅
[使用 DASH.js 在 HTML5 应用程序中嵌入 MPEG-DASH 自适应流式处理视频](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js 存储库](https://github.com/Dash-Industry-Forum/dash.js)

