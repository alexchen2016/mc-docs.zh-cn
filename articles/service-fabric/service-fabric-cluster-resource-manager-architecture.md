---
title: Resource Manager 体系结构 | Azure
description: Service Fabric 群集 Resource Manager 的体系结构概述。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 08/18/2017
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0e02b8824e932d3dfa9612f6591b475e35b6c0d5
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626965"
---
# <a name="cluster-resource-manager-architecture-overview"></a>群集 Resource Manager 体系结构概述
Service Fabric 群集资源管理器是在群集中运行的中心服务。 它管理群集中服务所需的状态，对资源消耗和任何放置规则而言尤其如此。 

为了管理群集中的资源，Service Fabric 群集资源管理器必须包含一些相关的信息：

- 当前存在的服务
- 每个服务的当前（或默认）资源消耗 
- 剩余的群集容量 
- 群集中节点的容量 
- 每个节点可消耗的资源量

给定服务的资源消耗量会随时间变化，服务通常会关注多种类型的资源。 在不同的服务之间，可能会同时测量实际物理资源和物理资源。 服务可能会跟踪内存和磁盘使用情况等物理指标。 更常见的是，服务可能会关注逻辑指标，例如“WorkQueueDepth”或“TotalRequests”。 可以同时在同一群集中使用逻辑指标和物理指标。 指标可在许多服务间共享，也可特定于特定服务使用。

## <a name="other-considerations"></a>其他注意事项
有时，群集的所有者和操作员与服务和应用程序创建者不同，或至少是一人身兼多职。 开发应用程序时，需要知道有关应用程序需求的一些内容。 需要估计应用程序将占用的资源以及不同服务的部署方法。 例如，Web 层需要在连接到 Internet 的节点上运行，而数据库服务则不必。 再举一例，Web 服务可能会受 CPU 和网络限制，而数据层服务更关注内存和磁盘使用情况。 但是，处理该服务在生产环境中的实时站点事件的人员，或者管理服务升级的人员，需要执行不同的作业，并且需要不同的工具。 

群集和服务都是动态的：

- 群集中的节点数可能增加也可能减少
- 不同大小和类型的节点可能加入也可能离开
- 可以创建、删除服务，并更改其所需的资源分配和放置规则
- 升级或其他管理操作可以在基础结构级别的应用程序中运行
- 随时可能会发生故障。

## <a name="cluster-resource-manager-components-and-data-flow"></a>群集 Resource Manager 组件和数据流
群集资源管理器必须跟踪每个服务的需求以及这些服务中每个服务对象的资源消耗。 群集资源管理器具有两个概念部件：在每个节点上运行的代理和容错服务。 每个节点上的代理会跟踪服务的负载报告、聚合这些报告，并定期汇报。 群集 Resource Manager 服务会聚合来自本地代理的所有信息，并根据当前配置做出反应。

请查看下图：

<center>

![资源均衡器体系结构][Image1]
</center>

运行时阶段可能会发生很多更改。 例如，假设某些服务使用的资源量更改、某些服务失败以及某些节点加入并离开群集。 节点上的所有更改都要进行汇总，并定期发送到群集 Resource Manager 服务（1、2），并在其中再次聚合、分析和存储。 每隔几秒钟，服务就查看更改，并确定是否需要任何操作 (3)。 例如，它可能注意到某些空节点已添加到群集。 因此，确定要将某些服务移到这些节点。 群集 Resource Manager 可能还注意到特定节点已超载，或者某些服务已失败或已删除，在其他位置释放了资源。

请查看下图，了解接下来会发生什么。 假设群集资源管理器确定需要更改。 它与其他系统服务（尤其是故障转移管理器）进行协调，进行必要的更改。 然后将所需命令发送到相应节点 (4)。 例如，假设资源管理器注意到节点 5 已超载，因此确定要将服务 B 从节点 5 移动到节点 4。 重新配置 (5) 结束时，群集看起来像这样：

<center>

![资源均衡器体系结构][Image2]
</center>

## <a name="next-steps"></a>后续步骤
- 群集 Resource Manager 提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇[描述 Service Fabric 群集](./service-fabric-cluster-resource-manager-cluster-description.md)的文章
- 群集资源管理器的主要职责是重新均衡群集，并强制执行放置规则。 有关如何配置这些行为的详细信息，请参阅[均衡 Service Fabric 群集](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!--Update_Description: update meta properties -->