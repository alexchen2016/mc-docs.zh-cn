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
ms.openlocfilehash: 9f5323a9d773e6f260d5937522078ef68d37454d
ms.sourcegitcommit: 878a2d65e042b466c083d3ede1ab0988916eaa3d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835810"
---
## <a name="launch-azure-local-shell"></a>启动 Azure 本地 Shell

[!INCLUDE [updated-for-az.md](../includes/updated-for-az.md)]

<!--Not Available on To open the Cloud Shell, just select **Try it** from the upper right corner of a code block. You can also launch Cloud Shell in a separate browser tab by going to [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Select **Copy** to copy the blocks of code, paste it into the Cloud Shell, and press enter to run it.-->

## <a name="get-the-managed-image"></a>获取托管映像

可使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 查看资源组中可用的映像列表。 了解映像名称及其所在的资源组后，就可以再次使用 `Get-AzImage` 来获取映像对象并将其存储在变量中以便以后使用。 此示例将从“myResourceGroup”资源组获取名为“myImage”的映像，并将其分配给变量“$managedImage”。 

```powershell
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。 库名称在你的订阅中必须唯一。 

使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 创建映像库。 以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库。

```powershell
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'China North'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```

## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 若要详细了解可以为映像定义指定的值，请参阅[映像定义](/virtual-machines/windows/shared-image-galleries#image-definitions)。

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 在此示例中，库映像名为 myGalleryImage。

```powershell
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="create-an-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 根据托管映像创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，映像版本为 1.0.0，该版本被复制到中国北部和中国东部数据中心。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。

```powershell
$region1 = @{Name='China East';ReplicaCount=1}
$region2 = @{Name='China North';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 要查看作业的进度，请键入 `$job.State`。

```powershell
$job.State
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。 
>
> 也可将映像版本存储在[区域冗余存储](/storage/common/storage-redundancy-zrs)中，方法是在创建映像版本时添加 `-StorageAccountType Standard_ZRS`。
>