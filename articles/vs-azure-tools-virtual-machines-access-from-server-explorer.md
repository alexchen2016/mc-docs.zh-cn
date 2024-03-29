---
title: 从服务器资源管理器访问 Azure 虚拟机 | Microsoft Docs
description: 概述如何在 Visual Studio 的服务器资源管理器中查看、创建和管理 Azure 虚拟机 (VM)。
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 08/31/2017
ms.date: 09/10/2018
ms.author: v-junlch
ms.openlocfilehash: 7ea08f8c44bf1501050264b0f3665d562c4e429d
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52644765"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>从服务器资源管理器访问 Azure 虚拟机

如果有 Azure 托管的虚拟机，可以在服务器资源管理器中访问它们。 必须首先登录到 Azure 订阅才能查看移动服务。 若要登录，请在服务器资源管理器中打开“Azure”节点的快捷菜单，并选择“连接到 Azure”。

1. 在 Cloud Explorer 中选择虚拟机，并选择 F4 键显示其属性窗口。

    下表显示了可用的属性，但这些属性都是只读的。 若要更改这些属性，请使用 [Azure 门户](https://portal.azure.cn)。

   | 属性 | 说明 |
   | --- | --- |
   | DNS 名称 |包含虚拟机 Internet 地址的 URL。 |
   | 环境 |对于虚拟机，此属性的值始终为“生产”。 |
   | Name |虚拟机的名称。 |
   | 大小 |虚拟机的大小，此值反映可用的内存和磁盘空间量。 有关详细信息，请参阅[虚拟机大小](/cloud-services/cloud-services-sizes-specs)。 |
   | 状态 |值包括“正在启动”、“已启动”、“正在停止”、“已停止”和“正在检索”状态。 如果出现“正在检索状态”，则表示当前状态未知。 此属性的值不同于 [Azure 门户](https://portal.azure.cn)上使用的值。 |
   | SubscriptionID |Azure 帐户的订阅 ID。 可以通过在 [Azure 门户](https://portal.azure.cn)上查看订阅的属性来显示此信息。 |
2. 选择一个终结点节点，并查看“属性”  窗口。
3. 下表描述了可用的终结点属性，但这些属性都是只读的。 若要添加或编辑虚拟机的终结点，请使用 [Azure 门户](https://portal.azure.cn)。 

   | 属性 | 说明 |
   | --- | --- |
   | Name |终结点的标识符。 |
   | 专用端口 |应用程序的内部网络访问端口。 |
   | 协议 |此终结点的传输层使用的协议：TCP 或 UDP。 |
   | 公用端口 |用于公开访问应用程序的端口。 |

<!-- Update_Description: update metedata properties -->