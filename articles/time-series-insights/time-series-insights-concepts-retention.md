---
title: 了解 Azure 时序见解环境中的数据保留 | Microsoft Docs
description: 本文介绍控制 Azure 时序见解环境中的数据保留的两项设置。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: 82588952d7db06e33810f08685c99a948f4ce53a
ms.sourcegitcommit: 41a1c699c77a9643db56c5acd84d0758143c8c2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58349121"
---
# <a name="understand-data-retention-in-time-series-insights"></a>了解时序见解中的数据保留

本文介绍影响时序见解 (TSI) 环境中的数据保留的两项设置。

## <a name="video"></a>视频： 

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>在本视频中，我们将介绍时序见解数据保留以及如何规划它。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

每个 TSI 环境都有一项设置控制“数据保留时间”。 该时间值的范围为 1 到 400 天。 将根据环境存储容量或保留期限 (1-400) 删除数据，以先达到的条件为准。

每个 TSI 环境都有一项附加设置：“超出存储限制时的行为”。 此设置控制达到环境最大容量时的传入和清除行为。 可以从两种行为中进行选择：
- 清除旧数据（默认行为）  
- 暂停传入

> [!NOTE]
> 创建新环境时，保留规则默认配置为“清除旧数据”。 在创建后，可以根据需要使用 Azure 门户或者在 TSI 环境的“配置”页上切换此设置。

有关切换保留行为的信息，请查看[在时序见解中配置保留](time-series-insights-how-to-configure-retention.md)。

比较数据保留行为：

## <a name="purge-old-data"></a>清除旧数据
- 此行为是 TSI 环境的默认行为，展示的行为与公共预览版 TSI 环境的行为相同。  
- 如果用户希望在其 TSI 环境中始终能够看到最近的数据，则此行为是首选行为。 
- 达到环境的限制（保留时间、大小或计数，以先达到的为准）时，此行为会立即清除数据。 默认情况下，保留时间设置为 30 天。 
- 最旧的引入数据最先清除（FIFO 方法）。

### <a name="example-1"></a>示例 1：
假设某个示例环境的保留行为是“持续传入和清除旧数据”：在此示例中，“数据保留时间”设置为 400 天。 “容量”设置为 S1 单位，包含 30 GB 总容量。   假设每天的入站数据累积平均为 500 MB。 根据给定的入站数据率，此环境只能保留 60 天的数据，因为在 60 天后会达到最大容量。 入站数据累积为：每天 500 MB x 60 天 = 30 GB。 

在此示例中，从第 61 天开始，环境会显示最新数据，但会清除超过 60 天的旧数据。 清除操作能够为流入的新数据腾出空间，因此能够继续浏览新数据。 

如果用户想要将数据保留更长时间，可以通过添加更多的单位或推送更少的数据来增加环境的大小。  

### <a name="example-2"></a>示例 2：
假设某个环境的保留行为也配置为“继续传入和清除旧数据”。 在此示例中，“数据保留时间”设置为一个较小值：180 天。 “容量”设置为 S1 单位，包含 30 GB 总容量。 若要存储 180 天的数据，每天的传入量不能超过 0.166 GB (166 MB)。  

只要此环境的每日传入率超过 0.166 GB，就无法将数据存储 180 天，因为某些数据将被清除。 在繁忙的时段，可以考虑使用这种环境。 假设环境的传入率可能会增大到每日平均 0.189 GB。 在繁忙时段，将会保留大约 158 天的数据（30 GB/0.189 = 保留期 158.73 天）。 此时间小于所需的数据保留时间范围。

## <a name="pause-ingress"></a>暂停传入
- 此行为旨在确保在达到数据保留期之前达到大小和计数限制时，不会清除这些数据。  
- 在由于超过保留期而清除数据之前，此行为可让用户有更多的时间来增大其环境的容量
- 此行为有助于防止数据丢失，但如果暂停数据传入的持续时间超过事件源的保留期，则有可能会丢失最近的数据。
- 但是，一旦达到环境的最大容量，环境将会暂停数据传入，直到执行附加的操作： 
   - 增加环境的最大容量。 有关详细信息，请参阅[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)以添加更多缩放单元。
   - 达到了数据保留期并清除了数据，从而使环境低于其最大容量。

### <a name="example-3"></a>示例 3：
假设某个环境的保留行为配置为“暂停传入”。 在此示例中，“数据保留期”配置为 60 天。 “容量”设置为 3 个 S1 单位。 假设此环境的每日数据传入量为 2-GB。 在此环境中，一旦达到最大容量，传入就会暂停。 此时，环境会一直显示相同的数据集，直到传入恢复，或者启用了“继续传入”（这会清除旧数据，以便为新数据腾出空间）。 

当传入恢复时：
- 数据会根据事件源的接收顺序流入
- 根据事件的时间戳为事件编制索引，除非超出了针对事件源的保留策略。 有关事件源保留配置的详细信息，请参阅[事件中心常见问题解答](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> 应设置警报，以提供通知来帮助避免传入暂停。 由于 Azure 事件源的默认保留期为 1 天，因此可能丢失数据。 一旦传入暂停，除非采取其他措施，否则可能丢失最近的数据。 必须增大容量，或者将行为切换到“清除旧数据”才能避免潜在的数据丢失。

在受影响的事件中心，请考虑调整“消息保留”属性，以最大程度地减少在时序见解中发生传入暂停时丢失数据的情况。

![事件中心消息保留。](media/time-series-insights-contepts-retention/event-hub-retention.png)

如果未在事件源中配置任何属性 (timeStampPropertyName)，TSI 默认为事件中心的抵达时间戳（X 轴）。 如果 timeStampPropertyName 配置为其他值，则在分析事件时，环境会查找数据包中配置的 timeStampPropertyName。 

如果需要扩展环境以包含更多容量或延长保留持续时间，请参阅[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)了解详细信息。  

## <a name="next-steps"></a>后续步骤
有关切换保留行为的信息，请查看[在时序见解中配置保留](time-series-insights-how-to-configure-retention.md)。
