---
title: 快速入门 - 在 Azure 中创建专用 Docker 注册表 - PowerShell
description: 快速了解如何使用 PowerShell 在 Azure 中创建专用 Docker 容器注册表。
services: container-registry
author: rockboyfor
ms.service: container-registry
ms.topic: quickstart
origin.date: 01/22/2019
ms.date: 04/15/2019
ms.author: v-yeche
ms.custom: seodec18, mvc
ms.openlocfilehash: 4397369db29ad0e3ce30d04ea534a3e0fbee20da
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529425"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建专用容器注册表

Azure 容器注册表是托管的专用 Docker 容器注册表服务，用于生成、存储和提供 Docker 容器映像。 本快速入门介绍如何使用 PowerShell 创建 Azure 容器注册表。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本快速入门需要 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可确定已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

还必须在本地安装 Docker。 Docker 提供的包适用于 [macOS][docker-mac]、[Windows][docker-windows] 和 [Linux][docker-linux] 系统。

<!--Not Available on Azure Cloud Shell-->

## <a name="sign-in-to-azure"></a>登录 Azure

使用 [Connect-AzAccount -Environment AzureChinaCloud][Connect-AzAccount -Environment AzureChinaCloud] 命令登录到 Azure 订阅，并按屏幕说明操作。

```powershell
Connect-AzAccount -Environment AzureChinaCloud
```

## <a name="create-resource-group"></a>创建资源组

通过 Azure 进行身份验证以后，请使用 [New-AzResourceGroup][New-AzResourceGroup] 创建资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
New-AzResourceGroup -Name myResourceGroup -Location ChinaEast
```

## <a name="create-container-registry"></a>创建容器注册表

接下来，请使用 [New-AzContainerRegistry][New-AzContainerRegistry] 命令在新的资源组中创建容器注册表。

注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例创建名为“myContainerRegistry007”的注册表。 替换以下命令中的 *myContainerRegistry007*，然后运行该命令以创建注册表：

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

本快速入门将创建一个“基本”注册表。该注册表已针对成本进行优化，是可供开发人员了解 Azure 容器注册表的选项。 有关可用服务层的详细信息，请参阅[容器注册表 SKU][container-registry-skus]。

## <a name="log-in-to-registry"></a>登录到注册表

在推送和拉取容器映像之前，必须登录到注册表。 在生产方案中，应该使用个人标识或服务主体进行容器注册表访问，但为了简洁起见，本快速入门使用 [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] 命令在注册表上启用管理员用户：

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

接下来，运行用于登录的 [docker login][docker-login] 命令：

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

该命令在完成后返回 `Login Succeeded`。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

用完在本快速入门中创建的资源后，请通过 [Remove-AzResourceGroup][Remove-AzResourceGroup] 命令删除资源组、容器注册表以及其中存储的容器映像：

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure PowerShell 创建 Azure 容器注册表、推送容器映像，以及提取和运行注册表中的映像。

<!--Not Available on  Continue to the Azure Container Registry tutorials for a deeper look at ACR.-->
<!--Not Available on  > [!div class="nextstepaction"]-->
<!--Not Available on  > [Azure Container Registry tutorials][container-registry-tutorial-quick-task]-->

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount -Environment AzureChinaCloud]: https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: https://docs.microsoft.com/powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: https://docs.microsoft.com/powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup

<!--Not Available on [container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md-->

[container-registry-skus]: container-registry-skus.md

<!-- Update_Description: wording update, update meta properties -->
