---
title: 适用于虚拟网络的 Azure CLI 示例 | Azure
description: 适用于虚拟网络的 Azure CLI 示例。
services: virtual-network
documentationcenter: virtual-network
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
origin.date: 04/17/2019
ms.date: 06/10/2019
ms.author: v-yeche
ms.openlocfilehash: 7a285f60effae60170b3a5273bbfccb68c2d33ac
ms.sourcegitcommit: df1b896faaa87af1d7b1f06f1c04d036d5259cc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66250477"
---
# <a name="azure-cli-samples-for-virtual-network"></a>适用于虚拟网络的 Azure CLI 示例

下表包含指向使用 Azure CLI 命令的 bash 脚本的链接：

| | |
|----|----|
| [为多层应用程序创建虚拟网络](./scripts/virtual-network-cli-sample-multi-tier-application.md) | 创建包含前端和后端子网的虚拟网络。 传入前端子网的流量仅限 HTTP 和 SSH，而传入后端子网的流量限于 MySQL、端口 3306。 |
| [两个对等虚拟网络](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 在同一区域中创建并连接两个虚拟网络。 |
| [通过网络虚拟设备的路由流量](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | 创建包含前端和后端子网的虚拟网络，以及可在这两个子网之间路由流量的 VM。 |
| [筛选入站和出站 VM 网络流量](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | 创建包含前端和后端子网的虚拟网络。 前端子网的入站网络流量仅限于 HTTP、HTTPS 和 SSH。 不允许从后端子网到 Internet 的出站流量。 |

<!--Not Available on [Configure IPv4 + IPv6 dual stack virtual network](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)-->
<!--ms.date: 05/07/2018-->