---
title: 将内容传送到客户 | Microsoft Docs
description: 本主题概述使用 Azure 媒体服务传送内容所涉及的操作。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 05/20/2019
ms.author: v-jay
ms.openlocfilehash: a01257efdbc0c30e76cc278070ce997d1e023334
ms.sourcegitcommit: a0b9a3955cfe3a58c3cd77f2998631986a898633
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65549986"
---
# <a name="deliver-content-to-customers"></a>向客户传送内容
向客户传送流或视频点播内容时，目标在于向处于不同网络条件下的各种设备传送优质视频。

若要实现此目标，可以：

* 将流编码为多比特率（自适应比特率）视频流。 此操作可负责处理质量和网络条件问题。
* 使用 Azure 媒体服务[动态打包](media-services-dynamic-packaging-overview.md)功能将流重新动态打包为不同的协议。 此操作可负责处理不同设备上的流式处理问题。 媒体服务支持以下自适应比特率流式处理技术的传送： <br/>
    * HTTP Live Streaming (HLS) - 向 URL 的“/Manifest”部分添加“(format=m3u8-aapl)”路径，告知流式处理源服务器返回供 Apple iOS 本机设备使用的 HLS 内容（有关详细信息，请参阅[定位符](#locators)和[URL](#URLs)）；
    * MPEG DASH - 向 URL 的“/Manifest”部分添加“(format=mpd-time-csf)”路径，告知流式处理源服务器返回 MPEG-DASH（有关详细信息，请参阅[定位符](#locators)和[URL](#URLs)）；
    * 平滑流式处理。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 

本文概述重要的内容传送概念。

若要查看已知问题，请参阅[已知问题](media-services-deliver-content-overview.md#known-issues)。

## <a name="dynamic-packaging"></a>动态打包
借助媒体服务提供的动态打包功能，可采用媒体服务支持的流式传输格式（MPEG-DASH、HLS、平滑流式处理）传送自适应比特率 MP4 或平滑流式处理编码内容，而不必重新打包成这些流式传输格式。 我们建议使用动态打包功能传送内容。

若要利用动态打包，需将夹层（源）文件编码为一组自适应比特率 MP4 文件或自适应比特率平滑流式处理文件。

借助动态打包功能，可存储和播放使用单一存储格式的文件。 媒体服务会根据请求生成并提供适当的响应。

动态打包适用于标准和高级流式处理终结点。 

有关详细信息，请参阅[动态打包](media-services-dynamic-packaging-overview.md)。

## <a name="filters-and-dynamic-manifests"></a>筛选器和动态清单
借助媒体服务，可为资产定义筛选器。 这些筛选器是服务器端规则，可帮助客户完成类似如下的操作：播放视频的特定部分，或指定客户设备可以处理的一个子集的音频和视频呈现形式（而非与该资产相关的所有呈现形式）。 通过客户请求根据一个或多个指定的筛选器流式传输视频时创建的 *动态清单* ，可实现此筛选操作。

有关详细信息，请参阅[筛选器和动态清单](media-services-dynamic-manifest-overview.md)。

## <a name="a-idlocatorslocators"></a><a id="locators"/>定位符
若要为用户提供一个可用于流式传输内容或下载内容的 URL，首先需要通过创建定位符来发布资产。 定位符提供访问资产中所含文件的入口点。 媒体服务支持两种类型的定位符：

* OnDemandOrigin 定位符。 这些定位符用于流媒体（例如，MPEG-DASH、HLS 或平滑流式处理）或渐进式下载文件。
* 共享访问签名 (SAS) URL 定位符。 这些定位符用于将媒体文件下载到本地计算机。

访问策略用于定义客户端可以访问特定资产的权限（例如读取、写入和列出）和持续时间。 请注意，创建 OnDemandOrigin 定位符时，不应使用列出权限 (AccessPermissions.List)。

定位符具有过期日期。 Azure 门户将定位符的过期日期设置为 100 年以后。

> [!NOTE]
> 如果在 2015 年 3 月之前使用 Azure 门户创建定位符，这些定位符设置为两年后过期。
> 
> 

若要更新定位符的过期日期，请使用 [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) 或 [.NET](https://go.microsoft.com/fwlink/?LinkID=533259) API。 请注意，更新 SAS 定位符的过期日期时，URL 会发生变化。

定位符不用于管理按用户的访问控制。 通过数字版权管理 (DRM) 解决方案，可以为不同的用户提供不同的访问权限。 有关详细信息，请参阅 [保护媒体](https://msdn.microsoft.com/library/azure/dn282272.aspx)。

创建定位符时，可能会由于 Azure 存储中所需存储和传播进程的影响，出现 30 秒的延迟。

## <a name="adaptive-streaming"></a>自适应流
自适应比特率技术允许视频播放器应用程序确定网络条件并从多个比特率中选择。 网络通信质量下降时，客户端可以选择较低的比特率，以便能够以较低的视频质量继续播放视频。 网络条件改善时，客户端可以切换到较高的比特率，提高视频质量。 Azure 媒体服务支持以下自适应比特率技术：HTTP Live Streaming (HLS)、平滑流式处理 和 MPEG-DASH。

若要为用户提供流式处理 URL，必须先创建一个 OnDemandOrigin 定位符。 通过创建定位符，可获得包含要流式传输的内容的资产的基本路径。 但是，为了能够流式传输此内容，需要进一步修改此路径。 若要构造流式处理清单文件的完整 URL，必须将定位符的路径值与清单 (filename.ism) 文件名连接起来。 然后，向定位符路径追加/Manifest 和相应的格式（如果需要）。

> [!NOTE]
> 也可通过 SSL 连接流式传输内容。 为此，请确保流 URL 以 HTTPS 开头。 请注意，目前 AMS 对自定义域不支持 SSL。  
> 

仅当要从中传送内容的流式处理终结点是在 2014 年 9 月 10 日之后创建的情况下，才可以通过 SSL 流式传输内容。 如果流式处理 URL 基于 2014 年 9 月 10 日之后创建的流式处理终结点，则 URL 会包含“streaming.mediaservices.chinacloudapi.cn”。 包含“origin.mediaservices.chinacloudapi.cn”（旧格式）的流式处理 URL 不支持 SSL。 如果 URL 采用旧格式，并且希望能够通过 SSL 流式传输内容，请创建新的流式处理终结点。 使用基于新流式处理终结点的 URL 通过 SSL 流式传输内容。

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>流式处理 URL 格式

### <a name="mpeg-dash-format"></a>MPEG-DASH 格式
{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.chinacloudapi.cn/{定位符 ID}/{文件名}.ism/Manifest(format=mpd-time-csf) 

<http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf>)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4 格式
{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.chinacloudapi.cn/{定位符 ID}/{文件名}.ism/Manifest(format=m3u8-aapl)

<http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl>)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3 格式
{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.chinacloudapi.cn/{定位符 ID}/{文件名}.ism/Manifest(format=m3u8-aapl-v3)

<http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3>)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) 格式带有仅音频筛选器
默认情况下，仅音频轨道已包括在 HLS 清单中。 这是针对手机网络进行 Apple 应用商店认证所必需的。 在这种情况下，如果客户端没有足够的带宽或者通过 2G 连接进行连接，则播放轨道会切换成仅音频。 这有助于让内容保持流式传输而无需缓冲，但没有视频内容。 在某些情况下，相对于仅播放音频而言，用户更愿意选择缓冲播放视频。 如果希望删除仅音频轨道，可在 URL 中添加 **audio-only=false** 。

<http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false>)

有关详细信息，请参阅 [Dynamic Manifest Composition support and HLS output additional features](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)（动态清单组合支持和其他 HSL 输出功能）。

### <a name="smooth-streaming-format"></a>平滑流格式
{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.chinacloudapi.cn/{定位符 ID}/{文件名}.ism/Manifest

示例：

http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>平滑流式处理 2.0 清单（旧清单）
默认情况下，平滑流式处理清单格式包含重复标记（r 标记）。 但是，一些播放器不支持 r 标记。 使用这些播放器的客户端可以使用禁用 r 标记的格式：

{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

<http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20>)

## <a name="progressive-download"></a>渐进式下载
通过渐进式下载，可在下载完整个文件之前开始播放媒体。 无法渐进式下载 .ism* (ismv、isma、ismt 或 ismc) 文件。

若要渐进式下载内容，请使用 OnDemandOrigin 类型的定位符。 以下示例演示了基于 OnDemandOrigin 类型的定位符的 URL：

http://amstest1.streaming.mediaservices.chinacloudapi.cn/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

必须解密希望从源服务进行流式传输的所有存储加密资产，才能进行渐进式下载。

## <a name="download"></a>下载
要将内容下载到客户端设备，必须创建 SAS 定位符。 使用 SAS 定位符可以访问文件所在的 Azure 存储容器。 要构建下载 URL，必须将文件名嵌入到主机和 SAS 签名之间。

以下示例演示了基于 SAS 定位符的 URL：

https://test001.blob.core.chinacloudapi.cn/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

请注意以下事项：

* 必须解密希望从源服务进行流式传输的所有存储加密资产，才能进行渐进式下载。
* 未在 12 小时内完成的下载会失败。

## <a name="streaming-endpoints"></a>流式处理终结点

流式处理终结点表示一个流服务，该服务可以直接将内容分发给客户端播放器应用程序，也可以直接将内容分发给内容分发网络 (CDN) 以进一步分发。 流式处理终结点服务的出站流可以是实时流，也可以是媒体服务帐户中的视频点播资产。 有两种类型的流式处理终结点，**标准**和**高级**。 有关详细信息，请参阅： [流式处理终结点概述](media-services-streaming-endpoints-overview.md)。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。  若要开始对内容进行流式处理并利用动态打包和动态加密功能，必须确保要从其流式获取内容的流式处理终结点处于“正在运行”状态。 

## <a name="known-issues"></a>已知问题
### <a name="changes-to-smooth-streaming-manifest-version"></a>更改为平滑流式处理清单版本
在 2016 年 7 月的服务版本之前，使用 Media Encoder Standard 或早期的 Azure 媒体编码器生成的资产通过动态打包功能进行流式处理，此时返回的平滑流式处理清单会符合 2.0 版的要求。 在 2.0 版本中，片段持续时间不使用所谓的重复 ('r') 标记。 例如：


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

在 2016 年 7 月的服务版本中，生成的平滑流式处理清单遵从 2.2 版的要求，其片段持续时间使用重复标记。 例如：

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

一些旧平滑流式处理客户端可能不支持此重复标记，并且无法加载清单。 若要解决此问题，可以使用旧清单格式参数 (format=fmp4-v20)，或将客户端更新到支持重复标记的最新版本。 有关详细信息，请参阅[平滑流式处理 2.0](media-services-deliver-content-overview.md#fmp4_v20)。

## <a name="related-topics"></a>相关主题
[轮转存储密钥后更新媒体服务定位符](media-services-roll-storage-access-keys.md)

<!--Update_Description: wording update-->