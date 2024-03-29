---
title: 使用 PowerShell 在 Azure 云服务中启用诊断 | Azure
description: 了解如何使用 PowerShell 为云服务启用诊断
services: cloud-services
documentationCenter: .net
authors: Thraka
manager: timlt
editor: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 09/06/2016
ms.date: 05/27/2019
ms.author: v-yiso
ms.openlocfilehash: b693fe2ec5b934d8b615bff2f0ccf27121b7b92f
ms.sourcegitcommit: 99ef971eb118e3c86a6c5299c7b4020e215409b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65829235"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>使用 PowerShell 在 Azure 云服务中启用诊断

可以使用 Azure 诊断扩展从云服务收集应用程序日志、性能计数器等诊断数据。 本文介绍如何使用 PowerShell 为云服务启用 Azure 诊断扩展。  有关本文所需的先决条件，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>在部署云服务过程中启用诊断扩展
此方法适合持续集成类型的方案，在这些方案中，可以在部署云服务的过程中启用诊断扩展。 创建新的云服务部署时，可以通过向 *New-AzureDeployment* cmdlet 传入 [ExtensionConfiguration](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) 参数启用诊断扩展。 *ExtensionConfiguration* 参数取值为可使用 [New-AzureServiceDiagnosticsExtensionConfig](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet 创建的诊断配置数组。

以下示例演示如何为某个云服务（其中的 WebRole 和 WorkerRole 拥有不同的诊断配置）启用诊断。

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 
```

如果诊断配置文件使用某个存储帐户名称指定了 `StorageAccount` 元素，则 `New-AzureServiceDiagnosticsExtensionConfig` cmdlet 会自动使用该存储帐户。 要使其工作，存储帐户需要与要部署的云服务位于同一订阅中。

从 Azure SDK 2.6 开始，MSBuild 发布目标输出生成的扩展配置文件将基于服务配置文件 (.cscfg) 中指定的诊断配置字符串包括存储帐户名称。 以下脚本演示了在部署云服务时如何分析发布目标输出中的扩展配置文件并为每个角色配置诊断扩展。

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
#Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
$roleName = ""
$roles = $extPath -split ".",0,"simplematch"
if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
    $roleName = $roles[1] 
    $x = 2
    while ($x -le $roles.Length)
        {
           if ($roles[$x] -ne "PubConfig")
            {
                $roleName = $roleName + "." + $roles[$x]
            }
            else
            {
                break
            }
            $x++
        }
    $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
    $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
    $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online 使用类似的方法通过诊断扩展自动部署云服务。 有关完整示例，请参阅 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) 。

如果在诊断配置中未指定 `StorageAccount` ，则需要将 *StorageAccountName* 参数传递给 cmdlet。 如果指定了 *StorageAccountName* 参数，cmdlet 始终使用该参数中指定的存储帐户，而不使用诊断配置文件中指定的存储帐户。

如果诊断存储帐户与云服务属于不同的订阅，则需要将 StorageAccountName 和 StorageAccountKey 参数显式传递给 cmdlet。 当诊断存储帐户在同一订阅中时，不需要 *StorageAccountKey* 参数，因为 cmdlet 可以在启用诊断扩展时自动查询和设置密钥值。 但是，如果诊断存储帐户在不同的订阅中，则 cmdlet 可能无法自动获取密钥，需要通过 *StorageAccountKey* 参数显式指定该密钥。

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>在现有的云服务上启用诊断扩展
可以使用 [Set-AzureServiceDiagnosticsExtension](https://docs.microsoft.com/zh-cn//powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet 在已运行的云服务上启用或更新诊断配置。

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name 
```

## <a name="get-current-diagnostics-extension-configuration"></a>获取当前诊断扩展配置
使用 [Get AzureServiceDiagnosticsExtension](https://docs.microsoft.com/zh-cn//powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet 可以获取云服务的当前诊断配置。

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>删除诊断扩展
若要在云服务上关闭诊断，可以使用 [Remove-AzureServiceDiagnosticsExtension](https://docs.microsoft.com/zh-cn//powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet。

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果使用不带 Role 参数的 Set-AzureServiceDiagnosticsExtension 或 New-AzureServiceDiagnosticsExtensionConfig 启用了诊断扩展，则可以使用不带 Role 参数的 Remove-AzureServiceDiagnosticsExtension 删除该扩展。 如果启用扩展时使用了 *Role* 参数，则删除扩展时也必须使用该参数。

若要从单个角色中删除诊断扩展，请使用以下命令：

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure 诊断和其他方法排查问题的详细说明，请参阅[在 Azure 云服务和虚拟机中启用诊断](./cloud-services-dotnet-diagnostics.md)。
* [诊断配置架构](/azure-monitor/platform/diagnostics-extension-schema-1dot2) 介绍了诊断扩展的各种 XML 配置选项。
* 若要了解如何为虚拟机启用诊断扩展，请参阅 [使用 Azure Resource Manager 模板创建具有监视和诊断功能的 Windows 虚拟机](../virtual-machines/windows/extensions-diagnostics-template.md)
