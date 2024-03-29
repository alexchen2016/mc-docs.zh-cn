---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 04/25/2019
ms.date: 05/20/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 519cb1819d3afa2aba08883973c2b74ac85a1fc6
ms.sourcegitcommit: 878a2d65e042b466c083d3ede1ab0988916eaa3d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835837"
---
## <a name="update-resources"></a>更新资源

对于能够更新的内容，存在一些限制。 以下项目可以更新： 

共享映像库：
- 说明

映像定义：
- 建议的 vCPU 数
- 建议的内存
- 说明
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域数
- 从最新项中排除
- 生命周期终结日期

如果打算添加副本区域，请勿删除源托管映像。 源托管映像是将映像版本复制到其他区域所需的。 

使用 [az sig update](https://docs.azure.cn/zh-cn/cli/sig?view=azure-cli-latest#az-sig-update) 更新对库的说明。 

```azurecli
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```

使用 [az sig image-definition update](https://docs.azure.cn/zh-cn/cli/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 更新对映像定义的说明。

```azurecli
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

使用 [az sig image-version update](https://docs.azure.cn/zh-cn/cli/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 更新要向其添加需复制的区域的映像版本。 此更改需要一定的时间，因为需将映像复制到新区域。

```azurecli
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=chinaeast
```

## <a name="delete-resources"></a>删除资源

需按相反的顺序删除资源，先删除映像版本。 删除所有映像版本以后，即可删除映像定义。 删除所有映像定义以后，即可删除库。 

使用 [az sig image-version delete](https://docs.azure.cn/zh-cn/cli/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete) 删除映像版本。

```azurecli
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

使用 [az sig image-definition delete](https://docs.azure.cn/zh-cn/cli/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete) 删除映像定义。

```azurecli
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

使用 [az sig delete](https://docs.azure.cn/zh-cn/cli/sig?view=azure-cli-latest#az-sig-delete) 删除映像库。

```azurecli
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```