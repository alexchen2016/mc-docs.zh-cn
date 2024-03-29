---
title: Azure 数据工厂中的 Validation 活动 | Microsoft Docs
description: Validation 活动在使用用户指定的某些条件验证附加的数据集之前，不会继续执行管道。
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 03/25/2019
ms.date: 06/10/2019
ms.author: v-jay
ms.openlocfilehash: 0538df517fdc913af656580a87b895daf2b21fc4
ms.sourcegitcommit: 1ebfbb6f29eda7ca7f03af92eee0242ea0b30953
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66732684"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Validation 活动
可以在管道中使用 Validation，以确保管道仅在验证附加数据集引用存在并满足指定条件或已达到超时后才继续执行。


## <a name="syntax"></a>语法

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | --------
name | “Validation”活动的名称 | String | 是 |
type | 必须设置为“验证”  。 | String | 是 |
dataset | 活动将阻止执行，直到它验证此数据集引用存在并满足指定的条件，或者达到超时为止。 提供的数据集应支持“MinimumSize”或“ChildItems”属性。 | 数据集引用 | 是 |
timeout | 指定活动运行的超时。 如果未指定值，则默认值为 7 天 ("7.00:00:00")。 格式为 d.hh:mm:ss | String | 否 |
sleep | 验证尝试之间的延迟（以秒为单位）。 如果未指定值，则默认值为 10 秒。 | Integer | 否 |
childItems | 检查文件夹是否包含子项目。 可以设置为 -true：验证文件夹是否存在以及它是否包含项目。 一直阻止，直到文件夹中至少存在一个项目或达到超时值为止。-false：验证文件夹是否存在以及它是否为空。 一直阻止，直到文件夹为空或达到超时值为止。 如果未指定值，则活动将一直阻止，直到文件夹存在或达到超时为止。 | 布尔 | 否 |
minimumSize | 文件的最小大小（以字节为单位）。 如果未指定值，则默认值为 0 字节 | Integer | 否 |


## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
