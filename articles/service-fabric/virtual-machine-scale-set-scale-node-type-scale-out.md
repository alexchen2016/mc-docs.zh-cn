---
title: 将节点类型添加到 Azure Service Fabric 群集 | Azure
description: 了解如何通过添加虚拟机规模集横向扩展 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/13/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 99874d768fc4bea5a2087497eb64735173d3a46f
ms.sourcegitcommit: f1ecc209500946d4f185ed0d748615d14d4152a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463613"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>通过添加虚拟机规模集扩大 Service Fabric 群集
本文介绍如何通过将新的节点类型添加到现有群集来扩大 Azure Service Fabric 群集。 Service Fabric 群集是通过网络连接在一起的一组虚拟机或物理机，可在其中部署和管理微服务。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过将新的节点类型（虚拟机规模集）添加到现有群集来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>将其它规模集添加到现有群集
将新的节点类型（由虚拟机规模集支持）添加到现有群集，该操作类似于[升级主节点类型](service-fabric-scale-up-node-type.md)，但不会使用相同的 NodeTypeRef；很显然，这不会禁用任何活跃使用的虚拟机规模集，并且如果不更新主节点类型，将不会失去群集可用性。 

NodeTypeRef 属性在虚拟机规模集 Service Fabric 扩展属性中声明：
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

此外，需要将此新节点类型添加到 Service Fabric 群集资源：

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>后续步骤
* 了解如何[纵向扩展主节点类型](service-fabric-scale-up-node-type.md)
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

<!-- Update_Description: update meta properties, wording update -->
