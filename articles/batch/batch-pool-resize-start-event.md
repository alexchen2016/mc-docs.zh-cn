---
title: Azure Batch 池调整大小开始事件 | Microsoft Docs
description: Batch 池调整大小开始事件参考。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: b95ea4a320a06d2862b38419ebc4c716f38dc2f5
ms.sourcegitcommit: c43ca3018ef00245a94b9a7eb0901603f62de639
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56987012"
---
# <a name="pool-resize-start-event"></a>池调整大小开始事件

 当池调整大小开始时，会发出此事件。 由于池调整大小是异步事件，预计在调整大小操作完成后会发出池调整大小完成事件。

 以下示例显示了池调整大小开始事件（即通过手动调整大小将池的大小从 0 调整为 2 个节点）的正文。

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|元素|类型|注释|
|-------------|----------|-----------|
|poolId|String|池的 id。|
|nodeDeallocationOption|String|指定何时从池中删除节点（如果池的大小正在减小）。<br /><br /> 可能的值包括：<br /><br /> **requeue** - 终止正在运行的任务并将其重新排队。 当作业启用时，任务会再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **terminate** - 终止正在运行的任务。 任务不会再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **taskcompletion** - 允许完成当前正在运行的任务。 等待时不计划任何新任务。 在所有任务完成时，删除节点。<br /><br /> **Retaineddata** - 允许完成当前正在运行的任务，并等待所有任务数据保留期到期。 等待时不计划任何新任务。 在所有任务保留期都已过期时，删除节点。<br /><br /> 默认值为 requeue。<br /><br /> 如果池的大小正在增加，该值会设置为**无效**。|
|currentDedicated|Int32|当前分配到池的计算节点数。|
|targetDedicated|Int32|池请求的计算节点数。|
|enableAutoScale|Bool|指定池大小是否随时间自动调整。|
|isAutoPool|Bool|指定是否已通过作业的 AutoPool 机制创建池。|

<!-- Update_Description: update metedata properties -->