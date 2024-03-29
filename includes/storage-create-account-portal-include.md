---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 05/06/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: ec0e7aa77a70f7128cee014ba5bb6a792bafac9e
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381817"
---
若要在 Azure 门户中创建常规用途 v2 存储帐户，请执行以下步骤：

1. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入“存储帐户”  。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”  。
1. 在显示的“存储帐户”窗口中，选择“添加”。  
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”  字段下，选择“新建”  。 输入新资源组的名称，如下图中所示。

    ![显示如何在门户中创建资源组的屏幕截图](./media/storage-create-account-portal-include/create-resource-group.png)

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置或使用默认位置。
1. 将这些字段设置为其默认值：

   |字段  |Value  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |性能     |标准         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |复制     |读取访问异地冗余存储 (LRS)         |
   |访问层     |热         |

1. 选择“查看+创建”  可查看存储帐户设置并创建帐户。
1. 选择“创建”  。

有关存储帐户类型和其他存储帐户设置的详细信息，请参阅 [Azure 存储帐户概述](/storage/common/storage-account-overview)。 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](/azure-resource-manager/resource-group-overview)。 
