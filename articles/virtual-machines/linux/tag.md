---
title: 如何标记 Azure Linux 虚拟机 | Azure
description: 了解如何标记使用 Resource Manager 部署模型在 Azure 中创建的 Azure Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
origin.date: 02/28/2017
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 18fd234e2b58d71dcc8419f9b8c0658add0f29f2
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674517"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中标记 Linux 虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Linux 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 15 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>使用 Azure CLI 进行标记

若要开始，需要安装最新的 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest) 并已使用 [az login](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

可以使用此命令查看给定虚拟机的所有属性，包括标记：

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm update` 命令以及标记参数 **--set**：

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

若要删除标记，可以在 `azure vm update` 命令中使用 **--remove** 参数。

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

既然我们已通过 Azure CLI 和门户将标记应用到资源中，那就让我们看一看使用情况详细信息，以在计费门户中的查看标记。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure Resource Manager 概述][Azure Resource Manager Overview]和[使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅[了解 Azure 帐单][Understanding your Azure Bill]。
<!-- Not Available on [Gain insights into your Azure resource consumption][Gain insights into your Azure resource consumption] -->

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing-understand-your-bill.md

<!-- Notice correct : [Understanding your Azure Bill]: ../../billing-understand-your-bill.md -->
<!-- Not Available on [Gain insights into your Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md-->

<!-- Update_Description: update meta properties, wording update, update link -->