---
title: 在 Azure 门户中创建 VM | Azure
description: 在 Azure 门户中创建 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 05/30/2017
ms.date: 05/21/2018
ms.author: v-yeche
ms.openlocfilehash: 86380dd64d5c4900e3238154b0396538b87afcb0
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52659329"
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>在 Azure 门户中创建运行 Windows 的虚拟机
> [!div class="op_single_selector"]
> * [Azure 门户](tutorial.md)
> * [PowerShell：经典部署](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 了解如何通过 **Azure 门户**[使用 Resource Manager 部署模型执行这些步骤](../../virtual-machines-windows-hero-tutorial.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

本教程演示如何在 Azure 门户中创建运行 Windows 的 Azure 虚拟机 (VM)。 我们会使用 Windows Server 映像作为示例，但这只是 Azure 提供的众多映像的其中一个。 请注意，映像的选择取决于订阅。 例如，Windows 桌面映像适用于 MSDN 订户。

本部分演示如何使用 Azure 门户中的“仪表板”选择并创建虚拟机。

也可以使用 [自己的映像](createupload-vhd.md)创建 VM。 若要了解此方法和其他方法，请参阅[创建 Windows 虚拟机的不同方式](../../virtual-machines-windows-creation-choices.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="createvirtualmachine"> </a>创建虚拟机
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>后续步骤
* 了解如何在 Azure 门户中[使用 Resource Manager 部署模型创建 VM](../../virtual-machines-windows-hero-tutorial.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。
* 登录到虚拟机。 有关说明，请参阅[登录到运行 Windows Server 的虚拟机](connect-logon.md)。
* 附加磁盘以存储数据。 可以附加空磁盘和包含数据的磁盘。 有关说明，请参阅[将数据磁盘附加到使用经典部署模型创建的 Windows 虚拟机](attach-disk.md)。

<!-- Update_Description: update meta properties -->