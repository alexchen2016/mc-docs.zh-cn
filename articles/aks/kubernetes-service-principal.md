---
title: 适用于 Azure Kubernetes 服务 (AKS) 的服务主体
description: 在 Azure Kubernetes 服务 (AKS) 中为群集创建和管理 Azure Active Directory 服务主体
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 04/25/2019
ms.date: 05/13/2019
ms.author: v-yeche
ms.openlocfilehash: d8dae9682c06a7653d071a5cbff5a4a0b4b7022c
ms.sourcegitcommit: 8b9dff249212ca062ec0838bafa77df3bea22cc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520699"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 的服务主体

AKS 群集需要 [Azure Active Directory (AD) 服务主体][aad-service-principal]才能与 Azure API 交互。 需要服务主体才能动态创建和管理其他 Azure 资源，例如 Azure 负载均衡器或容器注册表 (ACR)。

本文介绍如何创建和使用适用于 AKS 群集的服务主体。

## <a name="before-you-begin"></a>准备阶段

若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者预先创建一个可以与 AKS 群集配合使用的服务主体。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="automatically-create-and-use-a-service-principal"></a>自动创建和使用服务主体

使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，Azure 可以自动生成服务主体。

<!--Not Available on in the Azure portal or -->

在下述 Azure CLI 示例中，尚未指定服务主体。 在此方案中，Azure CLI 为 AKS 群集创建一个服务主体。 若要成功完成此操作，Azure 帐户必须具有创建服务主体所需的相应权限。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>手动创建服务主体

若要通过 Azure CLI 手动创建服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 在下一部分创建 AKS 群集时，会使用这些值。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>指定适用于 AKS 群集的服务主体

若要在通过 [az aks create][az-aks-create] 命令创建 AKS 群集时使用现有的服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令的输出中的 `--service-principal` 和 `--client-secret` 参数来指定 `appId` 和 `password`：

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

<!--Not Available on If you deploy an AKS cluster using the Azure portal, on the *Authentication* page of the **Create Kubernetes cluster** dialog, choose to **Configure service principal**. Select **Use existing**, and specify the following values:-->
<!--Not Available on - **Service principal client ID** is your *appId*-->
<!--Not Available on - **Service principal client secret** is the *password* value-->
<!--Not Available on ![Image of browsing to Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)-->

## <a name="delegate-access-to-other-azure-resources"></a>委托对其他 Azure 资源的访问权限

AKS 群集的服务主体可以用来访问其他资源。 例如，如果希望将 AKS 群集部署到现有 Azure 虚拟网络子网或连接到 Azure 容器注册表 (ACR)，则需要将对那些资源的访问权限委托给服务主体。

若要委托权限，请使用 [az role assignment create][az-role-assignment-create] 命令创建一个角色分配。 将 `appId` 分配到特定的作用域，例如一个资源组或虚拟网络资源。 然后，通过角色定义服务主体对资源的具体权限，如以下示例所示：

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

资源的 `--scope` 需要是完整的资源 ID，例如 */subscriptions/\<guid\>/resourceGroups/myResourceGroup* 或 */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

以下各部分详述了可能需要使用的常见委托。

### <a name="azure-container-registry"></a>Azure 容器注册表

如果使用 Azure 容器注册表 (ACR) 作为容器映像存储，则需授予 AKS 群集读取和拉取映像的权限。 必须向 AKS 群集的服务主体委托注册表的“读者”角色。 有关详细步骤，请参阅[向 AKS 授予对 ACR 的访问权限][aks-to-acr]。

### <a name="networking"></a>网络

可以使用高级网络，在该网络中，虚拟网络和子网或公共 IP 地址位于另一资源组中。 分配下列角色权限集之一：

- 创建一个[自定义角色][rbac-custom-role]，并定义以下角色权限：
    - *Microsoft.Network/virtualNetworks/subnets/join/action*
    - *Microsoft.Network/virtualNetworks/subnets/read*
    - *Microsoft.Network/publicIPAddresses/read*
    - *Microsoft.Network/publicIPAddresses/write*
    - *Microsoft.Network/publicIPAddresses/join/action*
- 或者，在虚拟网络的子网上分配[网络参与者][rbac-network-contributor]内置角色

### <a name="storage"></a>存储

可能需要访问另一资源组中的现有磁盘资源。 分配下列角色权限集之一：

- 创建一个[自定义角色][rbac-custom-role]，并定义以下角色权限：
    - *Microsoft.Compute/disks/read*
    - *Microsoft.Compute/disks/write*
- 或者，在资源组中分配[存储帐户参与者][rbac-storage-contributor]内置角色

### <a name="azure-container-instances"></a>Azure 容器实例

如果使用虚拟 Kubelet 与 AKS 集成并选择在与 AKS 群集分开的资源组中运行 Azure 容器实例 (ACI)，则必须在 ACI 资源组上授予 AKS 服务主体“参与者”权限。

## <a name="additional-considerations"></a>其他注意事项

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

- Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
- 默认情况下，服务主体凭据的有效期为一年。 你随时可以[更新或轮换服务主体凭据][update-credentials]。
- 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 *https://www.contoso.org/example*）相关联。 应用程序的 URL 不一定是实际的终结点。
- 指定服务主体**客户端 ID** 时，请使用 `appId` 的值。
- 在 Kubernetes 群集的代理节点 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中
- 使用 [az aks create][az-aks-create] 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 `~/.azure/aksServicePrincipal.json` 文件中。
- 删除通过 [az aks create][az-aks-create] 创建的 AKS 群集时，不会删除自动创建的服务主体。
    - 若要删除服务主体，请查询群集 *servicePrincipalProfile.clientId*，然后使用 [az ad app delete][az-ad-app-delete] 进行删除。 将以下资源组和群集名称替换为你自己的值：

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Active Directory 服务主体，请参阅[应用程序和服务主体对象][service-principal]。

有关如何更新凭据的信息，请参阅[为 AKS 中的服务主体更新或轮换凭据][update-credentials]。

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: https://docs.azure.cn/zh-cn/cli/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: https://docs.azure.cn/zh-cn/cli/ad/app?view=azure-cli-latest#az-ad-app-list
[az-ad-app-delete]: https://docs.azure.cn/zh-cn/cli/ad/app?view=azure-cli-latest#az-ad-app-delete
[az-aks-create]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md