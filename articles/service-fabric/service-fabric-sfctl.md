---
title: Azure Service Fabric CLI- sfctl | Azure
description: 介绍 Service Fabric CLI sfctl 命令。
services: service-fabric
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
origin.date: 12/06/2018
ms.date: 01/07/2019
ms.author: v-yeche
ms.openlocfilehash: b11c6c9aae11f5192c48657b8b0896582bc27a82
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083592"
---
# <a name="sfctl"></a>sfctl
用于管理 Service Fabric 群集和实体的命令。 此版本与 Service Fabric 6.4 运行时兼容。

命令遵循名词谓词模式。 有关详细信息，请参阅子组。

## <a name="subgroups"></a>子组
|子组|说明|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | 创建、删除和管理应用程序及应用程序类型。 |
| [chaos](service-fabric-sfctl-chaos.md) | 启动、停止和报告混沌测试服务。 |
| [cluster](service-fabric-sfctl-cluster.md) | 选择、管理和运行 Service Fabric 群集。 |
| [compose](service-fabric-sfctl-compose.md) | 创建、删除和管理 Docker Compose 应用程序。 |
| [容器](service-fabric-sfctl-container.md) | 在群集节点上运行与容器相关的命令。 |
| [is](service-fabric-sfctl-is.md) | 查询并向基础结构服务发送命令。 |
| [node](service-fabric-sfctl-node.md) | 管理构成群集的节点。 |
| [partition](service-fabric-sfctl-partition.md) | 查询和管理任何服务的分区。 |
| [property](service-fabric-sfctl-property.md) | 在 Service Fabric 名称下存储和查询属性。 |
| [replica](service-fabric-sfctl-replica.md) | 管理属于服务分区的副本。 |
| [rpm](service-fabric-sfctl-rpm.md) | 查询并向修复管理器服务发送命令。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | 管理独立 Service Fabric 群集。 |
| [service](service-fabric-sfctl-service.md) | 创建、删除和管理服务、服务类型与服务包。 |
| [设置](service-fabric-sfctl-settings.md) | 配置此 sfctl 实例的本地设置。 |
| [store](service-fabric-sfctl-store.md) | 针对群集映像存储执行基本文件级别操作。 |

<!--Not Available on [mesh](service-fabric-sfctl-mesh.md)-->

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。

<!--Update_Description: update meta properties, wording update, update meta properties -->