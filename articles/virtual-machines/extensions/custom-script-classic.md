---
title: 使用经典部署模型完成的适用于 Windows 的自定义脚本扩展
description: 通过使用自定义脚本扩展在远程 Windows VM 上运行 PowerShell 脚本自动执行 Azure VM 配置任务
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
origin.date: 01/17/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 3444178ef6ff7e75f00080a2b1429fab91e1abe8
ms.sourcegitcommit: 1ea0f453e7dcaef67f3c52747778c7f3b82e3e38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277523"
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>使用经典部署模型完成的适用于 Windows 的自定义脚本扩展

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 了解如何 [使用 Resource Manager 模型执行这些步骤](custom-script-windows.md)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../includes/virtual-machines-classic-portal.md)]

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 自定义脚本扩展与 Azure Resource Manager 模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 运行它。

本文档详细说明了如何通过 Azure PowerShell 模块和 Azure Resource Manager 模板使用自定义脚本扩展，同时详细说明了 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

可以针对 Windows Server 2008 R2、2012、2012 R2 和 2016 版本运行适用于 Windows 的自定义脚本扩展。

### <a name="script-location"></a>脚本位置

该脚本需存储在 Azure 存储中，或者存储在可以通过有效 URL 访问的任何其他位置。

### <a name="internet-connectivity"></a>Internet 连接

适用于 Windows 的自定义脚本扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示自定义脚本扩展的架构。 扩展需要脚本位置（Azure 存储或其他具有有效 URL 的位置）以及命令才能执行。 如果使用 Azure 存储作为脚本源，则需要 Azure 存储帐户名称和帐户密钥。 这些项目应视为敏感数据，并在扩展的受保护设置配置中指定。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| 扩展 | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行自定义脚本扩展。 若需包含自定义脚本扩展的示例模板，可访问 [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有的虚拟机。 有关详细信息，请参阅 [Set-AzVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmcustomscriptextension)。

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令。

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出将记录到在目标虚拟机的以下目录中发现的文件。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

脚本本身下载到目标虚拟机的以下目录中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>支持

如果对本文中的任何观点存在疑问，可以联系 [Azure 支持](https://support.azure.cn/zh-cn/support/contact)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://www.azure.cn/support/contact/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Azure 支持常见问题](https://www.azure.cn/support/faq/)。

<!-- Update_Description: update meta properties, udpate powershell az cmdlet -->