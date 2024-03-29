---
title: 异地复制的 Azure 容器注册表
description: 开始创建和管理异地复制的 Azure 容器注册表。
services: container-registry
author: rockboyfor
manager: digimobile
ms.service: container-registry
ms.topic: overview
origin.date: 04/10/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.openlocfilehash: 1f2f43ad1b11c855c27a01657a08b41e3e6a2987
ms.sourcegitcommit: 5bfa8ecc8a61eaf814437c78ea0d12214cabcb8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64929330"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure 容器注册表中的异地复制

需要本地状态或热备份的公司可选择从多个 Azure 区域运行服务。 最佳实践是在映像运行于的每个区域放置一个容器注册表，允许近网络操作，以实现快速可靠的映像层传输。 异地复制允许 Azure 容器注册表充当单个注册表，向多个区域提供多主区域注册表。

异地复制注册表有以下优点：

* 单个注册表/映像/标记的名称可跨多个区域使用
* 由区域部署实现近网络注册表访问
* 由于是从与容器主机处于相同区域的本地复制注册表中拉取映像，因此无额外传输费用
* 跨多个区域对注册表进行单一管理

> [!NOTE]
> 如果需要在多个 Azure 容器注册表中维护容器映像的副本，则 Azure 容器注册表还支持[映像导入](container-registry-import-images.md)。 
>

<!--Not Avaialble onFor example, in a DevOps workflow, you can import an image from a development registry to a production registry, without needing to use Docker commands.-->

## <a name="example-use-case"></a>示例用例
Contoso 在中国各地运行着一个公开展示网站。 为了向这些市场提供本地近网内容，Contoso 在中国北部、中国东部运行 [Azure Kubernetes 服务](/aks/) (AKS) 群集。 部署为 Docker 映像的网站应用程序在所有区域中均使用相同的代码和映像。 从在每个区域独特部署的数据库检索该区域的本地内容。 对于本地数据库这样的资源，每个区域部署均有其唯一配置。

开发团队位于北京，使用中国北部数据中心。

![推送到多个注册表](media/container-registry-geo-replication/before-geo-replicate.png)<br />*推送到多个注册表*

使用异地复制功能之前，Contoso 已在中国北部拥有基于 CN 的注册表，在中国东部拥有其他注册表。 为了向这些不同的区域提供服务，开发团队将映像推送到了两个不同的注册表。

```bash
docker push contoso.azurecr.cn/public/products/web:1.2
docker push contosochinaeast.azurecr.cn/public/products/web:1.2
```
![从多个注册表拉取](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*从多个注册表拉取*

多个注册表的典型挑战包括：

* 中国东部和中国北部均拉取自中国北部的注册表，当每个这样的远程容器主机从中国北部的数据中心拉取映像时，将产生出口费用。
* 开发团队必须将映像推送到中国北部和中国东部的注册表。
* 开发团队必须使用引用本地注册表的映像名称配置和维护每个区域的部署。
* 必须为每个区域配置注册表访问。

## <a name="benefits-of-geo-replication"></a>异地复制的优点

![从异地复制注册表拉取](media/container-registry-geo-replication/after-geo-replicate-pull.png)

使用 Azure 容器注册表的异地复制功能，将实现以下优点：

* 跨所有区域管理单个注册表：`contoso.azurecr.cn`
* 管理多个映像部署的单个配置，因为所有区域使用同一个映像 URL：`contoso.azurecr.cn/public/products/web:1.2`
* 由 ACR 管理异地复制（包括用于本地通知的区域 Webhook），推送到单个注册表

## <a name="configure-geo-replication"></a>配置异地复制
配置异地复制就如在地图上单击区域一样简单。

异地复制是[高级注册表](container-registry-skus.md)特有的功能。 如果尚未使用高级注册表，可在 [Azure 门户](https://portal.azure.cn)中将基本和标准更改为高级：

![在 Azure 门户中切换 SKU](media/container-registry-skus/update-registry-sku.png)

若要为高级注册表配置异地复制，可通过 https://portal.azure.cn 登录到 Azure 门户。

导航到 Azure 容器注册表，然后选择“复制”：

![Azure 门户容器注册表 UI 中的副本](media/container-registry-geo-replication/registry-services.png)

地图中显示了所有当前的 Azure 区域：

![Azure 门户中的区域地图](media/container-registry-geo-replication/registry-geo-map.png)

* 蓝色六边形表示当前的副本
* 绿色六边形表示可能的复制区域
* 灰色六边形表示尚不可复制的 Azure 区域

若要配置副本，请选择一个绿色六边形，然后选择“创建”：

![Azure 门户中的“创建副本”UI](media/container-registry-geo-replication/create-replication.png)

若要创建其他副本，请选择表示其他区域的绿色六边形，然后单击“创建”。

ACR 将开始在配置的副本间同步映像。 完成后，门户将显示“就绪”。 门户中的副本状态不会自动更新。 使用刷新按钮查看更新状态。

## <a name="geo-replication-pricing"></a>异地复制定价

异地复制是 Azure 容器注册表[高级 SKU](container-registry-skus.md) 的一项功能。 将注册表复制到所需区域时，每个区域都会产生高级注册表费用。

在前面的示例中，Contoso 将两个注册表合并到一起，并向中国东部和中国北部添加副本。 Contoso 每月将支付两次高级费用，且无额外配置或管理。 现在每个区域就从本地拉取映像，既提升了性能和可靠性，又节省了从中国北部到中国东部的网络传输费用。

<!--MOONCAKE: Correct on TWICE-->

## <a name="summary"></a>摘要

通过异地复制，可将区域数据中心作为一个全球云进行管理。 由于映像可跨多个 Azure 服务使用，因此不仅可以受益于单一管理平面，还可保持近网络、快速、可靠的本地映像拉取。

<!--Not Available on ## Next steps-->

<!--Not Available on [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md)-->

<!--Update_Description: new articles on container registry geo replication -->
<!--ms.date: 04/29/2019-->