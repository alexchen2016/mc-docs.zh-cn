---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: bfeddc0db549f760ed7ce33482bd7a124f2eedd7
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855457"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

在 AKS 群集的生命周期中，经常需要升级到最新的 Kubernetes 版本。 必须应用最新的 Kubernetes 安全版本，或者通过升级来获取最新功能。 本文演示如何升级现有的 AKS 群集。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.56 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 以下示例在名为 *myResourceGroup* 的资源组中检查是否有可供名为 *myAKSCluster* 的群集使用的升级：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，允许从 *1.10.x* ->  升级到 *1.11.x*，或者从 *1.11.x* ->  升级到 *1.12.x*，但不允许从 *1.10.x* ->  升级到 *1.12.x*。
>
> 若要从 *1.10.x* ->  升级到 *1.12.x*，请先从 *1.10.x* ->  升级到 *1.11.x*，然后从 *1.11.x* ->  升级到 *1.12.x*。

以下示例输出表明，群集可以升级到版本 *1.11.5* 或 *1.11.6*：

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 将向群集添加一个新节点，然后仔细地一次[隔离并清空][kubernetes-drain]一个节点，以将对正在运行的应用程序造成的中断情况降到最低。 以下示例将群集升级到版本 *1.11.6*：

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

升级群集需要几分钟时间，具体取决于有多少节点。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出表明群集现在运行 *1.11.6*：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

本文演示了如何升级现有的 AKS 群集。 若要详细了解如何部署和管理 AKS 群集，请参阅相关教程系列。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show