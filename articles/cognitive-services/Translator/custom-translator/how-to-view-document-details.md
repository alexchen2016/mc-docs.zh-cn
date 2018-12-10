---
title: 文档详细信息 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 此文档列表页面显示你的工作区中的前 10 个文档。 对于每个文档，它显示名称、配对、类型、语言、上传时间戳，以及上传了此文档的用户的电子邮件地址。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
origin.date: 11/13/2018
ms.date: 11/26/2018
ms.author: v-junlch
ms.topic: how to edit a model
ms.openlocfilehash: d2bc096ce2e1d10a245ebf293384012a0d18c222
ms.sourcegitcommit: bfd0b25b0c51050e51531fedb4fca8c023b1bf5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52673359"
---
# <a name="view-document-details"></a>查看文档详细信息

此文档列表页面显示你的工作区中的前 10 个文档。 对于每个文档，它显示名称、配对、类型、语言、上传时间戳，以及上传了此文档的用户的电子邮件地址。

单击单个文档可查看文档详细信息页。 文档详细信息页显示从文档提取的语句的列表。

- 默认情况下，下拉字段中会选中“源”语言，但可以切换为以目标语言查看语句。 
- 默认情况下，每页显示 20 个语句。 可以使用分页控件在页面之间进行浏览。

![文档详细信息](./media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>删除文档

用户必须是工作区所有者才能删除文档。 此外，如果文档由翻译过程或部署过程中的某个模型使用，则无法删除该文档。

1. 转到文档页
2.  将鼠标指针悬停在任何文档记录上，并单击垃圾桶图标。

    ![删除文档](./media/how-to/how-to-delete-document-1.png)

3.  确认删除。

    ![确认删除](./media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何训练模型](how-to-train-model.md)。
