---
title: 创建 Azure Data Lake Storage Gen2 存储帐户 | Microsoft Docs
description: 快速学习使用 Azure 门户、Azure PowerShell 或 Azure CLI 创建能够访问 Data Lake Storage Gen2 的新存储帐户。
services: storage
author: WenJason
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
origin.date: 12/06/2018
ms.date: 05/27/2019
ms.author: v-jay
ms.reviewer: jamesbak
ms.openlocfilehash: 741577cc7474f7bc9cb778383c5014828014ead4
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004117"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>快速入门：创建 Azure Data Lake Storage Gen2 存储帐户

Azure Data Lake Storage Gen2 [支持分层命名空间](data-lake-storage-introduction.md)，该命名空间提供了一个适合与 Hadoop 分布式文件系统 (HDFS) 配合使用的基于本机目录的文件系统。 可以通过 [ABFS 驱动程序](data-lake-storage-abfs-driver.md)从 HDFS 访问 Data Lake Storage Gen2 数据。

本快速入门展示了如何使用 [Azure 门户](https://portal.azure.cn/)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 或通过 [Azure CLI](/cli/?view=azure-cli-latest) 创建帐户。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)。 

|           | 先决条件 |
|-----------|--------------|
|门户     | 无         |
|PowerShell | 本快速入门需要 PowerShell 模块 Az.Storage 0.7 或更高版本  。 若要查找当前版本，请运行 `Get-Module -ListAvailable Az.Storage` 命令。 如果在运行此命令后，没有显示任何结果，或者如果出现 0.7 以外的版本，则必须升级 powershell 模块  。 请参阅本指南的[升级 powershell 模块](#upgrade-your-powershell-module)部分。
|CLI        | 可以安装 CLI 并在本地运行 CLI 命令。|

### <a name="install-the-cli-locally"></a>在本地安装 CLI

可在本地安装和使用 Azure CLI。 本快速入门需要运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>创建启用了 Azure Data Lake Storage Gen2 的存储帐户

在创建帐户前，首先创建一个资源组，使其充当你创建的存储帐户或任何其他 Azure 资源的逻辑容器。 若要清理本快速入门创建的资源，可以直接删除资源组。 删除资源组也会删除相关联的存储帐户，以及与资源组相关联的任何其他资源。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。

> [!NOTE]
> 必须将新的存储帐户创建为 **StorageV2(常规用途 v2 )** 类型才能利用 Data Lake Storage Gen2 功能。  

有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

为存储帐户命名时，请记住以下规则：

- 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
- 存储帐户名称在 Azure 中必须是唯一的。 没有两个存储帐户可以有相同的名称。

## <a name="create-an-account-using-the-azure-portal"></a>使用 Azure 门户创建帐户

登录到 [Azure 门户](https://portal.azure.cn)。

### <a name="create-a-resource-group"></a>创建资源组

若要在 Azure 门户中创建资源组，请执行以下步骤：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”。 
2.  单击“添加”按钮添加新的资源组。
3. 输入新资源组的名称。
4. 选择要在其中创建新资源组的订阅。
5. 选择资源组的位置。
6. 单击“创建”  按钮。  

   ![显示 Azure 门户中资源组创建情况的屏幕截图](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>创建常规用途 v2 存储帐户

若要在 Azure 门户中创建常规用途 v2 存储帐户，请执行以下步骤：

> [!NOTE]
> 分层命名空间目前在所有公共区域中提供。

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“所有服务”。  然后向下滚动到“存储”  ，接着选择“存储帐户”  。 在显示的“存储帐户”窗口中，选择“添加”。  
2. 选择之前创建的订阅和资源组   。
3. 输入存储帐户的名称。
4. 将“位置”设置为“中国东部”  
5. 将这些字段保留设置为其默认值：性能、帐户类型、复制、访问层     。
6. 选择要在其中创建存储帐户的订阅。
7. 选择“下一步:  高级 >”
8. 将“SECURITY”和“VIRTUAL NETWORKS”字段下的值设置为默认值   。
9. 在“Data Lake Storage Gen2”  部分中，将“分层命名空间”设置为“已启用”。  
10. 单击“查看 + 创建”以创建存储帐户  。

    ![显示 Azure 门户中存储帐户创建情况的屏幕截图](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

现在已通过门户创建了存储帐户。

### <a name="clean-up-resources"></a>清理资源

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。 
2. 找到要删除的资源组，右键单击列表右侧的“更多”按钮 ( **...** )。 
3. 选择“删除资源组”并进行确认。 

## <a name="create-an-account-using-powershell"></a>使用 PowerShell 创建帐户

首先，安装最新版本的 [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) 模块。

然后，升级 powershell 模块，登录到 Azure 订阅，创建资源组，然后创建存储帐户。

### <a name="upgrade-your-powershell-module"></a>升级 powershell 模块

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 PowerShell 与 Data Lake Storage Gen2 交互，需要安装模块 Az.Storage 0.7 或更高版本  。

首先使用提升的权限打开 PowerShell 会话。

安装 Az.Storage 模块

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

使用 `Login-AzAccount` 命令并按照屏幕上的说明进行身份验证。

```powershell
Login-AzAccount -EnvironmentName AzureChinaCloud
```

### <a name="create-a-resource-group"></a>创建资源组

若要通过 PowerShell 创建新的资源组，请使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令： 

> [!NOTE]
> 分层命名空间目前在所有公共区域中提供。

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "chinaeast"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>创建常规用途 v2 存储帐户

若要使用本地冗余存储 (LRS) 从 PowerShell 创建常规用途 v2 存储帐户，请使用 [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) 命令：

```powershell
$location = "chinaeast"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>清理资源

若要删除资源组及其关联的资源（包括新的存储帐户），请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>使用 Azure CLI 创建帐户

若要登录到本地安装的 CLI，请运行登录命令：

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>为 Azure Data Lake Gen 2 添加 CLI 扩展

若要使用 CLI 来与 Data Lake Storage Gen2 交互，必须将扩展添加到 shell。

为此，请使用本地 shell 输入以下命令：`az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>创建资源组

若要通过 Azure CLI 创建新的资源组，请使用 [az group create](/cli/group) 命令。

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location chinaeast
```

> [!NOTE]
> > 分层命名空间目前在所有公共区域中提供。

### <a name="create-a-general-purpose-v2-storage-account"></a>创建常规用途 v2 存储帐户

若要使用本地冗余存储从 Azure CLI 创建常规用途 v2 存储帐户，请使用 [az storage account create](/cli/storage/account) 命令。

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location chinaeast `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>清理资源

若要删除资源组及其关联的资源（包括新的存储帐户），请使用 [az group delete](/cli/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建了一个具有 Data Lake Storage Gen2 功能的存储帐户。 若要了解如何通过存储帐户上传和下载 Blob，请参阅以下主题。

* [AzCopy V10](/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
