---
title: Azure 网络观察程序中的“IP 流验证”简介 | Azure
description: 此页概述了网络观察程序“IP 流验证”功能
services: network-watcher
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 12/24/2018
ms.date: 12/24/2018
ms.author: v-lingwu
ms.openlocfilehash: f640c8204312a5e94ff5abf3f5b1399e67997009
ms.sourcegitcommit: b7cefb6ad34a995579a42b082dcd250eb79068a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890180"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure 网络观察程序中的“IP 流验证”简介

“IP 流验证”检查是允许还是拒绝进出虚拟机的数据包。 这些信息包括方向、协议、本地 IP、远程 IP、本地端口和远程端口。 如果数据包被安全组拒绝，则返回拒绝数据包的规则的名称。 虽然可以选择任何源或目标 IP，“IP 流验证”功能可帮助管理员快速诊断与 Internet 的连接问题，以及与本地环境的连接问题。

IP 流验证查看应用于网络接口的所有网络安全组 (NSG) 的规则，例如子网或虚拟机 NIC。 然后，将基于网络接口的配置设置验证流量流。 “IP 流验证”功能可用于确认网络安全组中的规则是否正在阻止进出虚拟机的入口或出口流量。

需要在要运行“IP 流验证”的所有区域中创建网络观察程序实例。 网络观察程序是一个区域性服务，只能针对同一区域中的资源运行。 所使用的实例不会影响“IP 流验证”的结果，因为仍将返回与 NIC 或子网关联的任何路由。

![1][1]

## <a name="next-steps"></a>后续步骤

请访问以下文章，通过门户了解是允许还是拒绝特定虚拟机的数据包。 [通过门户使用“IP 流验证”检查是否允许 VM 上的流量](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

<!--Update_Description: new articles on network watcher ip flow verify overview -->