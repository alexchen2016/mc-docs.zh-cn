---
title: 如何使用 Media Encoder Standard 对 Azure 资产进行编码 | Microsoft Docs
description: 了解如何使用 Media Encoder Standard 为 Azure 媒体服务上的媒体内容编码。 代码示例使用 REST API。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/20/2019
ms.date: 05/20/2019
ms.author: v-jay
ms.openlocfilehash: 5bb1da1a87a8a7c7ae98d10333ddfece734221cf
ms.sourcegitcommit: a0b9a3955cfe3a58c3cd77f2998631986a898633
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65549952"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>如何使用 Media Encoder Standard 对资产进行编码
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [门户](media-services-portal-encode.md)
>
>

## <a name="overview"></a>概述

若要通过 Internet 传送数字视频，必须压缩媒体文件。 数字视频文件较大，可能因过大而无法通过 Internet 传送或者无法在客户的设备上正常显示。 编码是压缩视频和音频以便客户能够查看媒体的过程。

编码作业是 Azure 媒体服务中最常见的处理操作之一。 可通过创建编码作业将媒体文件从一种编码转换为另一种编码。 编码时，可以使用媒体服务的内置编码器（Media Encoder Standard）。 还可使用媒体服务合作伙伴提供的编码器。 可通过 Azure 市场获取第三方编码器。 可以使用为编码器定义的预设字符串，或使用预设配置文件来指定编码任务的详细信息。 若要查看可用预设的类型，请参阅 [Media Encoder Standard 的任务预设](https://msdn.microsoft.com/library/mt269960)。

每个作业可以有一个或多个任务，具体因要完成的处理类型而异。 通过 REST API，可采用以下两种方式之一创建作业及相关任务：

* 可通过作业实体上的任务导航属性以内联方式定义任务。
* 使用 OData 批处理

建议始终将源文件编码为自适应比特率 MP4 集，然后使用[动态打包](media-services-dynamic-packaging-overview.md)将该集转换为所需格式。

如果输出资产已经过存储加密，则必须配置资产传送策略。 有关详细信息，请参阅[配置资产传送策略](media-services-rest-configure-asset-delivery-policy.md)。

## <a name="considerations"></a>注意事项

访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

开始引用媒体处理器之前，请确认媒体处理器 ID 正确无误。 有关详细信息，请参阅[获取媒体处理器](media-services-rest-get-media-processor.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

## <a name="create-a-job-with-a-single-encoding-task"></a>创建包含单个编码任务的作业

> [!NOTE]
> 使用媒体服务 REST API 时，需注意以下事项：
>
> 访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。
>
> 使用 JSON 并指定在请求中使用 __metadata 关键字（例如，为了引用某个链接对象）时，必须将 Accept 标头设置为 [JSON 详细格式](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)：Accept: application/json;odata=verbose。
>
>

以下示例说明了如何使用一个任务集来创建和发布作业，从而以特定分辨率和质量对视频进行编码。 使用 Media Encoder Standard 编码时，可以使用[此处](https://msdn.microsoft.com/library/mt269960)指定的任务配置预设。

请求：

    POST https://media.chinacloudapi.cn/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.chinacloudapi.cn

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.chinacloudapi.cn/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

响应：

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>设置输出资产的名称
以下示例说明了如何设置 assetName 属性：

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>注意事项
* TaskBody 属性必须使用文本 XML 来定义任务使用的输入资产或输出资产的数量。 任务文章包含 XML 的 XML 架构定义。
* 在 TaskBody 定义中，必须将 `<inputAsset>` 和 `<outputAsset>` 的每个内部值设置为 JobInputAsset(value) 或 JobOutputAsset(value)。
* 一个任务可以有多个输出资产。 一个 JobOutputAsset(x) 只能一次用作作业中任务的输出。
* 可以将 JobInputAsset 或 JobOutputAsset 指定为任务的输入资产。
* 任务不得构成循环。
* 传递给 JobInputAsset 或 JobOutputAsset 的 value 参数代表资产的索引值。 在作业实体定义的 InputMediaAssets 和 OutputMediaAssets 导航属性中定义实际资产。
* 由于媒体服务基于 OData v3，因此 InputMediaAssets 和 OutputMediaAssets 导航属性集合中的单个资产将通过“__metadata : uri”名称/值对进行引用。
* InputMediaAssets 映射到已在媒体服务中创建的一个或多个资产。 OutputMediaAssets 由系统创建。 它们不引用现有资产。
* 可使用 assetName 属性来命名 OutputMediaAssets。 如果该属性不存在，则 OutputMediaAsset 的名称为 `<outputAsset>` 元素的任意内部文本值，并以作业名称值或作业 ID 值（在没有定义名称属性的情况下）为后缀。 例如，如果将 assetName 的值设置为“Sample”，则会将 OutputMediaAsset 名称属性设置为“Sample”。 但是，如果未设置 assetName 的值，但已将作业名称设置为“NewJob”，则 OutputMediaAsset 名称为“JobOutputAsset(value)_NewJob”。

## <a name="create-a-job-with-chained-tasks"></a>创建包含连锁任务的作业
在许多应用程序方案中，开发人员希望创建一系列处理任务。 在媒体服务中，可以创建一系列连锁任务。 每个任务执行不同的处理步骤，并且可以使用不同的媒体处理器。 连锁任务可以将资产从一个任务转给另一个任务，从而对资产执行线性序列的任务。 但是，在作业中执行的任务不需要处于序列中。 创建连锁任务时，连锁 ITask 对象在单个 IJob 对象中创建。

> [!NOTE]
> 每个作业当前有 30 个任务的限制。 如果需要连锁 30 个以上的任务，请创建多个作业以包含任务。
>
>

    POST https://media.chinacloudapi.cn/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.chinacloudapp.cn/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>注意事项
若要启用任务链，必须满足以下条件：

* 一个作业必须包含至少两个任务。
* 必须至少有一个任务的输入是作业中另一个任务的输出。

## <a name="use-odata-batch-processing"></a>使用 OData 批处理
以下示例演示如何使用 OData 批处理来创建作业和任务。 有关批处理的信息，请参阅 [Open Data Protocol (OData) 批处理](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)。

    POST https://media.chinacloudapi.cn/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.chinacloudapi.cn


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.chinacloudapi.cn/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.chinacloudapi.cn/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.chinacloudapi.cn/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>使用 JobTemplate 创建作业
使用一组常用任务处理多个资产时，JobTemplate 可用于指定默认任务预设或设置任务顺序。

以下示例演示如何使用以内联方式定义的 TaskTemplate 创建 JobTemplate。 TaskTemplate 将 Media Encoder Standard 用作 MediaProcessor 来编码资产文件。 但是，也可使用其他 MediaProcessor。

    POST https://media.chinacloudapi.cn/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> 与其他媒体服务实体不同的是，必须为每个 TaskTemplate 定义一个新的 GUID 标识符并将其放入请求正文中的 taskTemplateId 和 ID 属性中。 内容标识方案必须遵循“标识 Azure 媒体服务实体”中所述的方案。 此外，不能更新 JobTemplate。 而必须使用更新的更改创建新的 JobTemplate。
>
>

如果成功，返回以下响应：

    HTTP/1.1 201 Created

    . . .


以下示例演示如何创建引用 JobTemplate Id 的作业：

    POST https://media.chinacloudapi.cn/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.chinacloudapi.cn/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


如果成功，返回以下响应：

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>要浏览的高级编码功能
* [如何生成缩略图](media-services-dotnet-generate-thumbnail-with-mes.md)
* [在编码期间生成缩略图](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [在编码期间剪辑视频](media-services-crop-video.md)
* [自定义编码预设](media-services-custom-mes-presets-with-dotnet.md)
* [使用图像叠加视频或给视频加水印](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="next-steps"></a>后续步骤
了解如何创建对资产进行编码的作业后，请参阅[如何使用媒体服务检查作业进度](media-services-rest-check-job-progress.md)。

## <a name="see-also"></a>另请参阅
[获取媒体处理器](media-services-rest-get-media-processor.md)

<!--Update_Description: add section "Advanced Encoding Features to explore" and modify "x-ms-version"-->
