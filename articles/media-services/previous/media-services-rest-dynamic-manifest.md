---
title: 使用 Azure 媒体服务 REST API 创建筛选器 | Microsoft Docs
description: 本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务创建动态清单来存档此选择性流。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
origin.date: 03/20/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: d3110753f88248deef05d47f635ea7fa24e185c6
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624167"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>使用 Azure 媒体服务 REST API 创建筛选器 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

从 2.17 版开始，可使用媒体服务为资产定义筛选器。 这些筛选器是服务器端规则，可让客户选择运行如下操作：只播放一段视频（而非播放完整视频），或只指定客户设备可以处理的一部分音频和视频再现内容（而非与该资产相关的所有再现内容）。 通过按客户请求创建的动态清单可以实现对资产进行这种筛选，并基于指定的筛选器流式传输视频。

有关与筛选器和动态清单相关的更多详细信息，请参阅[动态清单概述](media-services-dynamic-manifest-overview.md)。

本文介绍如何使用 REST API 创建、更新和删除筛选器。 

## <a name="types-used-to-create-filters"></a>用于创建筛选器的类型
创建筛选器时使用以下类型：  

* [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect 和 FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> 访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

## <a name="create-filters"></a>创建筛选器
### <a name="create-global-filters"></a>创建全局筛选器
若要创建全局筛选器，请使用以下 HTTP 请求：  

#### <a name="http-request"></a>HTTP 请求
请求标头

    POST https://media.chinacloudapi.cn/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.chinacloudapi.cn

请求正文 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>HTTP 响应
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>创建局部 AssetFilter
若要创建局部 AssetFilter，请使用以下 HTTP 请求：  

#### <a name="http-request"></a>HTTP 请求
请求标头

    POST https://media.chinacloudapi.cn/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.chinacloudapi.cn

请求正文 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>HTTP 响应
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>列出筛选器
### <a name="get-all-global-filters-in-the-ams-account"></a>获取 AMS 帐户中的所有全局 **筛选器**
若要列出筛选器，请使用以下 HTTP 请求： 

#### <a name="http-request"></a>HTTP 请求
    GET https://media.chinacloudapi.cn/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.chinacloudapi.cn

### <a name="get-assetfilters-associated-with-an-asset"></a>获取与资产关联的 **AssetFilter**。
#### <a name="http-request"></a>HTTP 请求
    GET https://media.chinacloudapi.cn/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.chinacloudapi.cn

### <a name="get-an-assetfilter-based-on-its-id"></a>基于 ID 获取 **AssetFilter**
#### <a name="http-request"></a>HTTP 请求
    GET https://media.chinacloudapi.cn/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>更新筛选器
使用 PATCH、PUT 或 MERGE 并结合新的属性值来更新筛选器。  有关这些操作的详细信息，请参阅 [PATCH、PUT、MERGE](https://msdn.microsoft.com/library/dd541276.aspx)。

如果更新筛选器，则流式处理终结点需要两分钟的时间来刷新规则。 如果内容是通过使用此筛选器提供的（并在代理和 CDN 缓存中缓存），则更新此筛选器会导致播放器失败。 请在更新筛选器之后清除缓存。 如果此选项不可用，请考虑使用其他筛选器。  

### <a name="update-global-filters"></a>更新全局筛选器
若要更新全局筛选器，请使用以下 HTTP 请求： 

#### <a name="http-request"></a>HTTP 请求
请求标头： 

    MERGE https://media.chinacloudapi.cn/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.chinacloudapi.cn
    Content-Length: 384

请求正文： 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>更新局部 AssetFilter
若要更新局部筛选器，请使用以下 HTTP 请求： 

#### <a name="http-request"></a>HTTP 请求
请求标头： 

    MERGE https://media.chinacloudapi.cn/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.chinacloudapi.cn

请求正文： 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>删除筛选器
### <a name="delete-global-filters"></a>删除全局筛选器
若要删除全局筛选器，请使用以下 HTTP 请求：

#### <a name="http-request"></a>HTTP 请求
    DELETE https://media.chinacloudapi.cn/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.chinacloudapi.cn


### <a name="delete-local-assetfilters"></a>删除局部 AssetFilter
若要删除局部 AssetFilter，请使用以下 HTTP 请求：

#### <a name="http-request"></a>HTTP 请求
    DELETE https://media.chinacloudapi.cn/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.chinacloudapi.cn 

## <a name="build-streaming-urls-that-use-filters"></a>生成使用筛选器的流式处理 URL
有关如何发布和传送资产的信息，请参阅[将内容传送到客户概述](media-services-deliver-content-overview.md)。

以下示例演示了如何将筛选器添加到流式处理 URL。

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**平滑流**

    http://testendpoint-testaccount.streaming.mediaservices.chinacloudapi.cn/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

## <a name="see-also"></a>另请参阅
[动态清单概述](media-services-dynamic-manifest-overview.md)
<!--Update_Description: x-ms-version: 2.17-->
