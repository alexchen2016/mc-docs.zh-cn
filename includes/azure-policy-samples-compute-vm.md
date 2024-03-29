---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 05/17/2018
ms.date: 01/14/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 06ab22e8f1a4a9c6a5a11b594e466289da0783a4
ms.sourcegitcommit: 4f91d9bc4c607cf254479a6e5c726849caa95ad8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53996471"
---
### <a name="virtual-machines"></a>虚拟机

|  |  |
|---------|---------|
| [允许自定义资源组中的 VM 映像](../articles/governance/policy/samples/allow-custom-vm-image.md) |  要求自定义映像来自已批准的资源组。 指定已批准的资源组的名称。 |
| [允许的存储帐户和虚拟网络的 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 要求存储帐户和虚拟机使用已批准的 SKU。 使用内置策略以确保使用已批准的 SKU。 指定已批准的虚拟机 SKU 数组和已批准的存储帐户 SKU 数组。 |
| [已批准的 VM 映像](../articles/governance/policy/samples/allowed-custom-images.md) | 要求在环境中仅部署已批准的自定义映像。 指定已批准的映像 ID 的数组。 |
| [如果扩展不存在，则进行审核](../articles/governance/policy/samples/audit-extension-not-exist.md) | 如果扩展不是通过虚拟机部署的，则进行审核。 指定扩展发布者和类型以检查是否已部署扩展。 |
| [不允许使用 VM 扩展](../articles/governance/policy/samples/not-allowed-vm-extension.md) | 禁止使用指定的扩展。 指定一个数组，其中包含被禁止的扩展类型。 |