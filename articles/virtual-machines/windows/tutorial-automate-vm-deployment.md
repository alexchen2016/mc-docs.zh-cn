---
title: 教程 - 在 Azure 中的 Windows VM 上安装应用程序 | Azure
description: 本教程介绍如何使用自定义脚本扩展运行脚本并将应用程序部署到 Azure 中的 Windows 虚拟机
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 11/29/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 772fd40a06619bec7653f92dc0d497e1a1f4e7dc
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56665929"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>教程 - 使用自定义脚本扩展将应用程序部署到 Azure 中的 Windows 虚拟机

若要以快速一致的方式配置虚拟机 (VM)，可以使用[适用于 Windows 的自定义脚本扩展](extensions-customscript.md)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用自定义脚本扩展安装 IIS
> * 创建使用自定义脚本扩展的 VM
> * 在应用扩展后查看正在运行的 IIS 站点

## <a name="launch-azure-local-shell"></a>启动 Azure 本地 Shell

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="custom-script-extension-overview"></a>自定义脚本扩展概述
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。

自定义脚本扩展与 Azure Resource Manager 模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

自定义脚本扩展适用于 Windows 和 Linux VM。

## <a name="create-virtual-machine"></a>创建虚拟机
使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```powershell
Connect-AzAccount -Environment AzureChinaCloud
$cred = Get-Credential
```

现在，可使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例在“ChinaEast”位置创建一个名为 myVM 的 VM。 如果资源组 *myResourceGroupAutomate* 和支持的网络资源不存在，则会创建它们。 此 cmdlet 还打开端口 *80*，目的是允许 Web 流量。

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "China East" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

创建资源和 VM 需要几分钟的时间。

## <a name="automate-iis-install"></a>自动安装 IIS
使用 [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) 安装自定义脚本扩展。 该扩展运行 `powershell Add-WindowsFeature Web-Server` 来安装 IIS Web 服务器，然后更新 Default.htm 页以显示 VM 的主机名：

```powershell
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "ChinaEast" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-web-site"></a>测试网站
使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取前面创建的 myPublicIPAddress 的 IP 地址：

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

然后，可将公共 IP 地址输入 Web 浏览器中。 网站随即显示，其中包括负载均衡器将流量分发到的 VM 的主机名，如下例所示：

![运行 IIS 网站](./media/tutorial-automate-vm-deployment/running-iis-website.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你在 VM 上自动执行了 IIS 安装。 你已了解如何：

> [!div class="checklist"]
> * 使用自定义脚本扩展安装 IIS
> * 创建使用自定义脚本扩展的 VM
> * 在应用扩展后查看正在运行的 IIS 站点

转到下一教程，了解如何创建自定义 VM 映像。

> [!div class="nextstepaction"]
> [创建自定义 VM 映像](./tutorial-custom-images.md)

<!--Update_Description: update meta properties, wording update-->