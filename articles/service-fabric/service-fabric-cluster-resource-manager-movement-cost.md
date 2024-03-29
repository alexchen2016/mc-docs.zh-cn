---
title: Service Fabric 群集资源管理器：移动成本 | Azure
description: Service Fabric 服务的移动成本概述
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 08/18/2017
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 7d6df55a150ea7e321dbca388064cc2c16fa3f30
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626303"
---
# <a name="service-movement-cost"></a>服务移动成本
尝试确定要对群集进行哪些更改时，Service Fabric 群集资源管理器考虑的一个因素是这些更改的成本。 “成本”这一概念根据能够改进的群集量而权衡。 移动服务以满足均衡、碎片整理和其他要求时，成本是一项考虑因素。 目标是以最稳妥或最便宜的方式满足这些要求。 

移动服务可将 CPU 时间和网络带宽的成本降到最低。 对于有状态服务，需要复制消耗额外内存和磁盘的这些服务的状态。 将 Azure Service Fabric 群集资源管理器所提出的解决方案成本降到最低有助于避免使用不必要的群集资源。 但是，也不希望忽略可大幅改善群集中资源分配的解决方案。

群集资源管理器提供两种方式来计算和限制成本，同时尝试管理群集。 第一种机制只需计算它所进行的每次移动。 如果生成的两个解决方案的均衡值（分数）大致相同，则群集资源管理器倾向于采用成本（移动总量）最低的解决方案。

此策略很适用。 但是，对于默认负载或静态负载，在任何复杂的系统中不太可能所有移动都一样。 有些移动的成本可能要昂贵得多。

## <a name="setting-move-costs"></a>设置移动成本 
可在服务创建时为其指定默认的移动成本：

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#： 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

此外，还可在服务创建后为该服务动态指定或更新 MoveCost： 

PowerShell： 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#：

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>动态指定每个副本的移动成本

前面的代码片段全部用于从服务本身外部同时指定整个服务的 MoveCost。 但是，当特定服务对象的移动成本在其生命周期内发生变化时，移动成本最为有用。 由于服务本身可能最了解移动给定时间的成本是多少，因此服务可以在运行时使用 API 来报告自己的移动成本。 

C#：

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>移动成本的影响
MoveCost 有四个级别：零、低、中和高。 MoveCost 是相互的，但零除外。 “零”移动成本表示移动不会产生成本，不应计入解决方案的分数。 将移动成本设置为“高”并不能确保副本始终呆在一个位置。

<center>

![选择要移动的副本时考虑到移动成本因素][Image1]
</center>

MoveCost 可帮助我们在达成对等的均衡时，查找整体导致最少中断且最容易实现的解决方案。 服务的成本概念可以相对于许多事项。 计算移动成本时的最常见因素包括：

- 服务必须移动的状态或数据量。
- 客户端断开连接的成本。 移动主要副本的成本通常比移动次要副本的成本更高。
- 中断某些进行中操作的成本。 某些数据存储级别的操作，或者为了响应客户端调用而执行的操作，成本都很高。 在特定的时间点后，除非有必要，否则我们不会停止这些操作。 因此，当操作正在进行时，提高该服务对象的移动成本可以降低其移动的可能性。 当操作完成之后，可以将成本重新设置为正常。

## <a name="enabling-move-cost-in-your-cluster"></a>在群集中启用移动成本
为了考虑到更为精细的 MoveCost，必须在群集中启用 MoveCost。 如不进行此设置，则会使用计数移动的默认模式来计算 MoveCost，并忽略 MoveCost 报告。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤
- Service Fabric 群集资源管理器使用指标管理群集中的消耗和容量。 有关指标以及如何配置指标的详细信息，请查看[在 Service Fabric 中使用指标管理资源消耗和负载](service-fabric-cluster-resource-manager-metrics.md)。
- 有关群集 Resource Manager 如何在群集中管理和均衡负载的详细信息，请查看[均衡 Service Fabric 群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!--Update_Description: update meta properties -->