---
title: Media Encoder Standard 格式和编解码器 - Azure
description: 本主题概述 Media Encoder Standard 格式和编解码器。
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
origin.date: 03/18/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: 6b5c3312fbdf1dd043247de7964fdc3a594498d1
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624123"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard 格式和编解码器

本文档包含最常见的导入和导出文件格式的列表，可以将这些格式用于 Media Encoder Standard。

## <a name="input-containerfile-formats"></a>输入容器/文件格式
| 文件格式（文件扩展名） | 支持 |
| --- | --- |
| FLV（使用 H.264 和 AAC 编解码器） (.flv) |是 |
| MXF    (.mxf) |是 |
| GXF    (.gxf) |是 |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、3gpp、.mpg) |是 |
| Windows Media 视频（WMV）/ASF (.wmv、.asf) |是 |
| AVI（8 位/10 位未压缩）(.avi) |是 |
| MP4（.mp4、.m4a、.m4v）/ISMV (.isma、.ismv) |是 |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |是 |
| Matroska/WebM (.mkv) |是 |
| WAVE/WAV (.wav) |是 |
| QuickTime (.mov) |是 |

> [!NOTE]
> 以上是较常见的文件扩展名的列表。 Media Encoder Standard 支持许多其他扩展名（例如：.m2ts、.mpeg2video、.qt）。 
> 
> 

### <a name="audio-formats-in-input-containers"></a>输入容器中的音频格式
Media Encoder Standard 支持在输入容器中带有以下音频格式：

* MXF、GXF 和 QuickTime 文件，其中的音频曲目具有交错的立体声或 5.1 示例

或

* MXF、GXF 和 QuickTime 文件，其中的音频以独立 PCM 轨道的形式携带，但可以从文件元数据推导频道映射（到立体声或 5.1 的映射）

## <a name="input-video-codecs"></a>输入视频编解码器
| 输入视频编解码器 | 支持 |
| --- | --- |
| AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra |8 位 4:2:0 和 4:2:2 |
| Avid DNxHD（MXF 格式） |是 |
| DVCPro/DVCProHD（MXF 格式） |是 |
| 数字视频 (DV)（AVI 文件格式） |是 |
| JPEG 2000 |是 |
| MPEG-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体） |最高支持 422 Profile |
| MPEG-1 |是 |
| VC-1/WMV9 |是 |
| Canopus HQ/HQX |否 |
| MPEG-4 第 2 部分 |是 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |是 |
| YUV420（未压缩或夹层） |是 |
| Apple ProRes 422 |是 |
| Apple ProRes 422 LT |是 |
| Apple ProRes 422 HQ |是 |
| Apple ProRes Proxy |是 |
| Apple ProRes 4444 |是 |
| Apple ProRes 4444 XQ |是 |
| HEVC/H.265| Main 和 Main 10 (&#42;) Profiles<br/>Main 10 Profile 支持适用于 8 位 4:2:0 内容。 |

## <a name="input-audio-codecs"></a>输入音频编解码器
| 输入音频编解码器 | 支持 |
| --- | --- |
| AAC（AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1） |是 |
| MPEG Layer 2 |是 |
| MP3 (MPEG-1 Audio Layer 3) |是 |
| Windows Media 音频 |是 |
| WAV/PCM |是 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |是 |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |是 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |是 |
| AMR（自适应多速率） |是 |
| AES（SMPTE 331M 和 302M、AES3-2003） |否 |
| Dolby® E |否 |
| Dolby® Digital (AC3) |否 |
| Dolby® Digital Plus (E-AC3) |否 |

## <a name="output-formats-and-codecs"></a>输出格式和编解码器
下表列出了支持导出操作的编解码器和文件格式。

| 文件格式 | 视频编解码器 | 音频编解码器 |
| --- | --- | --- |
| MP4 <br/><br/>（包括多比特率 MP4 容器） |H.264（High、Main 和 Baseline Profile） |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264（High、Main 和 Baseline Profile） |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="see-also"></a>另请参阅
[使用 Azure 媒体服务对所需内容进行编码](media-services-encode-asset.md)

[如何使用 Media Encoder Standard 进行编码](media-services-dotnet-encode-with-media-encoder-standard.md)

