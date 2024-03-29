---
title: Azure Media Indexer 的任务预设
description: 本主题概述 Azure Media Indexer 的任务预设。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 02/08/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 788760941896b49e575192303985765d43988e12
ms.sourcegitcommit: 7b93bc945ba49490ea392476a8e9ba1a273098e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833317"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure Media Indexer 的任务预设 

Azure Media Indexer 是用于执行以下任务的媒体处理器：使媒体文件和内容可搜索、生成隐藏式字幕跟踪和关键字、为属于资产的资产文件编制索引。

本主题介绍需要传入索引作业的任务预设。 有关完整示例，请参阅[使用 Azure Media Indexer 为媒体文件编制索引](media-services-index-content.md)。

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer 配置 XML

下表说明了配置 XML 的元素和属性。

|Name|必需|说明|
|---|---|---|
|输入|是|要编制索引的资产文件。<br/>Azure Media Indexer 支持以下格式的媒体文件：MP4、MOV、WMV、MP3、M4A、WMA、AAC、WAV。 <br/><br/>可以在 **input** 元素的 **name** 或 **list** 属性中指定文件名（如下所示）。 如果未指定要编制索引的资产文件，系统会选择主文件。 如果未设置主资产文件，则为输入资产中的第一个文件编制索引。<br/><br/>若要显式指定资产文件名，请执行：<br/>```<input name="TestFile.wmv" />```<br/><br/>还可以一次为多个资产文件编制索引（最多 10 个文件）。 为此，请按以下步骤操作：<br/>- 创建一个文本文件（清单文件），并为其指定扩展名 .lst。<br/>- 将输入资产中所有资产文件名的列表添加到此清单文件。<br/>- 将该清单文件添加（上传）到资产。<br/>- 在输入的列表属性中指定清单文件的名称。<br/>```<input list="input.lst">```<br/><br/>**注意：** 如果在清单文件中添加了 10 个以上的文件，则索引作业会失败并显示 2006 错误代码。|
|metadata|false|指定的资产文件的元数据。<br/>```<metadata key="..." value="..." />```<br/><br/>可以提供预定义键的值。 <br/><br/>当前支持以下键：<br/><br/>**title** 和 **description** - 用于更新语言模型，以改进语音识别准确性。<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** 和 **password** - 在通过 http 或 https 下载 Internet 文件时用于身份验证。<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>username 和 password 值应用到输入清单中的所有媒体 URL。|
|features<br/><br/>在版本 1.2 中添加。 目前，唯一支持的功能是语音识别（“ASR”）。|false|语音识别功能具有以下设置键：<br/><br/>Language：<br/>- 要在多媒体文件中识别的自然语言。<br/>- 英语、西班牙语<br/><br/>CaptionFormats：<br/>- 以分号分隔的所需输出字幕格式的列表（如果有）<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB：<br/>- 布尔标志，指定是否需要 AIB 文件（用于 SQL Server 和客户索引器 IFilter）。 有关详细信息，请参阅“在 Azure Media Indexer 和 SQL Server 中使用 AIB 文件”。<br/>- True；False<br/><br/>GenerateKeywords：<br/>- 布尔标志，指定是否需要关键字 XML 文件。<br/>- True；False。|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer 配置 XML 示例

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>后续步骤

参阅[使用 Azure Media Indexer 为媒体文件编制索引](media-services-index-content.md)。

