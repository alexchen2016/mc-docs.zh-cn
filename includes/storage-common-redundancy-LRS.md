---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 03/26/2018
ms.date: 09/10/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b43b3f6186c026255db39e760579f6eb01585218
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52658588"
---
本地冗余存储 (LRS) 设计为在一年中提供至少 99.999999999%（11 个 9）的对象持久性，它将数据复制到一个存储缩放单元中。 该存储缩放单元托管在你在其中创建存储帐户的区域中的一个数据中心内。 只有在数据已写入到所有副本后，到 LRS 存储帐户的写入请求才会成功返回。 这些副本各自驻留在同一存储缩放单元中的不同容错域和更新域中。

存储缩放单元是指存储节点的机架的集合。 容错域 (FD) 是一组表示出错的物理单元的节点，可将其视为属于同一物理机架的节点。 升级域 (UD) 是一组在服务升级（推出）过程中一起升级的节点。 副本分布在同一存储缩放单元内的各个 UD 和 FD 中。 此体系结构可以确保，当硬件故障影响单个机架时或者当节点在推广期间升级时，你的数据可用。

与其他选项相比，LRS 是成本最低的复制选项，提供的持久性也最低。 如果发生数据中心级灾难（例如火灾或洪灾），则所有副本都可能会丢失或无法恢复。 为了减轻此风险，我们建议使用异地冗余存储 (GRS)。

* 如果应用程序存储着在发生数据丢失时可轻松重构的数据，则可以选择 LRS。
* 由于数据治理需要，某些应用程序被限制为只能在一个国家/地区内复制数据。 在某些情况下，在其中为 GRS 帐户复制数据的配对区域可能在另一个国家/地区。