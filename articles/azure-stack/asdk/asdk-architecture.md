---
title: Azure Stack 开发工具包体系结构 | Microsoft Docs
description: 介绍 Azure Stack 开发工具包 (ASDK) 的体系结构。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimbile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/21/2019
ms.date: 03/18/2019
ms.author: v-jay
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0f8ab99e318d5efde5c15959d5ea2548fd2e2812
ms.sourcegitcommit: c5646ca7d1b4b19c2cb9136ce8c887e7fcf3a990
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2019
ms.locfileid: "57988092"
---
# <a name="azure-stack-development-kit-architecture"></a>Azure Stack 开发工具包体系结构
Azure Stack 开发工具包 (ASDK) 是在单个主计算机上运行的 Azure Stack 的单节点部署。 边缘路由组件安装在主计算机上，为 Azure Stack 提供 NAT 和 VPN 功能。 Azure Stack 基础结构角色在物理主计算机的 Hyper-V 层中运行。


## <a name="virtual-machine-roles"></a>虚拟机角色
ASDK 提供的服务使用托管在开发工具包主机上的以下 VM：

| Name | 说明 |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack 存储服务。|
| **AzS-ADFS01** | Active Directory 联合身份验证服务 (ADFS)。  |
| **AzS-CA01** | Azure Stack 角色服务的证书颁发机构服务。|
| **AzS-DC01** | Azure Stack 的 Active Directory、DNS 和 DHCP 服务。|
| **AzS-ERCS01** | 紧急恢复控制台 VM。 |
| **AzS-GWY01** | 边缘网关服务，例如租户网络的 VPN 站点到站点连接。|
| **AzS-NC01** | 用于管理 Azure Stack 网络服务的网络控制器。  |
| **AzS-SLB01** | Azure Stack 中用于租户和 Azure Stack 基础结构服务的负载均衡多路复用器服务。  |
| **AzS-SQL01** | Azure Stack 基础结构角色的内部数据存储。  |
| **AzS-WAS01** | Azure Stack 管理门户和 Azure 资源管理器服务。|
| **AzS-WASP01**| Azure Stack 用户（租户）门户和 Azure 资源管理器服务。|
| **AzS-XRP01** | Azure Stack 的基础结构管理控制器，包括计算、网络和存储资源提供程序。|


## <a name="next-steps"></a>后续步骤
[了解基本的 ASDK 管理任务](asdk-admin-basics.md)
