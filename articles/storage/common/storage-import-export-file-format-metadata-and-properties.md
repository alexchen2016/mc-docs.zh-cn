---
title: Azure 导入/导出元数据和属性文件格式 | Azure
description: 了解如何为导入或导出作业包含的一个或多个 Blob 指定元数据和属性。
author: hayley244
manager: digimobile
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/23/2017
ms.date: 08/28/2017
ms.author: v-haiqya
ms.openlocfilehash: 20c5f2f95e345f6031ab1b50f5bf1336836c42a9
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643760"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure 导入/导出服务元数据和属性文件格式
可将一个或多个 Blob 的元数据和属性指定为导入作业或导出作业的一部分。 要设置将创建为导入作业一部分的 Blob 的元数据或属性，应在包含所要导入数据的硬盘驱动器上提供一个元数据或属性文件。 对于导出作业，元数据和属性将写入到在返回的硬盘驱动器上包含的元数据或属性文件。  
  
## <a name="metadata-file-format"></a>元数据文件格式  
元数据文件的格式如下所示：  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML 元素|类型|说明|  
|-----------------|----------|-----------------|  
|`Metadata`|Root 元素|元数据文件的根元素。|  
|`metadata-name`|String|可选。 XML 元素指定 Blob 的元数据名称，其值指定元数据设置值。|  
  
## <a name="properties-file-format"></a>属性文件格式  
属性文件的格式如下：  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML 元素|类型|说明|  
|-----------------|----------|-----------------|  
|`Properties`|Root 元素|属性文件的根元素。|  
|`Last-Modified`|String|可选。 Blob 的上次修改时间。 仅适用于导出作业。|  
|`Etag`|String|可选。 Blob 的 ETag 值。 仅适用于导出作业。|  
|`Content-Length`|String|可选。 Blob 大小，以字节为单位。 仅适用于导出作业。|  
|`Content-Type`|String|可选。 Blob 的内容类型。|  
|`Content-MD5`|String|可选。 Blob 的 MD5 哈希。|  
|`Content-Encoding`|String|可选。 Blob 的内容编码。|  
|`Content-Language`|String|可选。 Blob 的内容语言。|  
|`Cache-Control`|String|可选。 Blob 的缓存控制字符串。|  

## <a name="next-steps"></a>后续步骤

有关设置 Blob 元数据和属性的详细规则，请参阅[设置 Blob 属性](https://docs.microsoft.com/rest/api/storageservices/set-blob-properties)、[设置 Blob 元数据](https://docs.microsoft.com/rest/api/storageservices/set-blob-metadata)以及[设置和检索 Blob 资源的属性与元数据](https://docs.microsoft.com/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources)。
<!--Update_Description: update link-->