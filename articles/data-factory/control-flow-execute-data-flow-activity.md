---
title: Azure 数据工厂中的执行数据流活动 | Microsoft Docs
description: 如何在数据工厂管道中执行数据流。
services: data-factory
documentationcenter: ''
author: WenJason
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/22/2019
ms.date: 06/10/2019
ms.author: v-jay
ms.openlocfilehash: 68fae32a21e33df439324837e59c2486e7fb13a2
ms.sourcegitcommit: 1ebfbb6f29eda7ca7f03af92eee0242ea0b30953
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66732696"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Azure 数据工厂中的执行数据流活动
使用执行数据流活动，在管道调试（沙盒）运行和管道触发的运行中运行 ADF 数据流。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>语法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Type 属性

* ```dataflow``` 是要执行的数据流实体的名称
* ```compute``` 描述 Spark 执行环境
* ```coreCount``` 是要分配给此活动执行（执行数据流）的核心数

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="debugging-pipelines-with-data-flows"></a>调试具有数据流的管道

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

使用“数据流调试”，利用预热的群集在管道调试运行中以交互方式测试数据流。 使用“管道调试”选项，在管道中测试数据流。

### <a name="run-on"></a>运行位置

这是一个必填字段，定义用于执行数据流活动的 Integration Runtime。 默认情况下，数据工厂将使用默认的自动解析 Azure Integration Runtime。 但是，你可以创建自己的 Azure Integration Runtime，用于定义执行数据流活动所需的具体区域、计算类型、核心计数和 TTL。

执行数据流所需的默认设置是采用 8 核心常规计算，TTL 为 60 分钟。

针对该数据流执行操作选择计算环境。 默认设置为 Azure 自动解析默认 Integration Runtime。 此选项会在数据工厂所在区域的 Spark 环境中执行数据流。 计算类型将是作业群集，这意味着计算环境需要数分钟才能启动。

你可以控制数据流活动的 Spark 执行环境。 在 [Azure Integration Runtime](concepts-integration-runtime.md) 中存在设置，用于设置计算类型（常规用途、内存优化、计算优化）、辅助角色核心数以及生存时间，使执行引擎符合数据流计算要求。 另外，设置 TTL 可以让群集始终处于预热状态，可以立即执行作业。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。  使用“调试”来调试具有数据流的管道时，会针对 8 核的默认 Spark 群集执行操作。

### <a name="staging-area"></a>暂存区域

如果将数据接收到 Azure 数据仓库中，则必须为 Polybase 批负载选择一个暂存位置。

## <a name="parameterized-datasets"></a>参数化数据集

如果使用参数化数据集，请确保设置参数值。

![执行数据流参数](media/data-flow/params.png "参数")

### <a name="debugging-parameterized-data-flows"></a>调试参数化数据流

只能通过执行数据流活动在管道调试运行中使用参数化数据集来调试数据流。 目前，ADF 数据流中的交互式调试会话不适用于参数化数据集。 管道执行和调试运行将可以使用参数。

一种好的做法是使用静态数据集构建数据流，这样就可以在设计时使用完整的元数据列传播。 然后，在实现数据流管道的可操作化以后，将静态数据集替换为动态参数化数据集。

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
