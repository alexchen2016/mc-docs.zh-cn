---
title: 在 Azure 中的 Windows VM 中运行 PowerShell 脚本
description: 本主题介绍了如何使用“运行命令”在 Azure Windows 虚拟机中运行 PowerShell 脚本。
services: automation
ms.service: automation
author: rockboyfor
ms.author: v-yeche
origin.date: 04/26/2019
ms.date: 05/20/2019
ms.topic: article
manager: digimobile
ms.openlocfilehash: 61c91f34a26d505e9bdd1546a51af677bdaaf5e3
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004562"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>使用“运行命令”在 Windows VM 中运行 PowerShell 脚本

“运行命令”使用 VM 代理在 Azure Windows VM 中运行 PowerShell 脚本。 这些脚本可以用于常规的计算机或应用程序管理，并且可以用来快速诊断和修正 VM 访问和网络问题并使 VM 恢复正常运行状态。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>优点

有多个选项可以用来访问虚拟机。 “运行命令”可以使用 VM 代理在虚拟机上以远程方式运行脚本。 可以通过 Windows VM 的 Azure 门户、[REST API](https://docs.microsoft.com/rest/api/compute/virtual%20machines%20run%20commands/runcommand) 或 [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) 使用“运行命令”。

此功能适用于要在虚拟机中运行脚本的所有方案，并且是排查和修正因网络或管理用户配置错误而未打开 RDP 或 SSH 端口的虚拟机的唯一方法。

## <a name="restrictions"></a>限制

使用“运行命令”时存在以下限制：

* 输出限制为最后的 4096 个字节
* 运行脚本的最短时间大约为 20 秒
* 脚本以系统身份在 Windows 上运行
* 一次只能运行一个脚本
* 不支持提示输入信息（交互模式）的脚本。
* 无法取消正在运行的脚本
* 脚本最多可以运行 90 分钟，之后它将超时
* 需要从 VM 建立出站连接才能返回脚本的结果。

> [!NOTE]
> 若要正常工作，运行命令需要连接（端口 443）到 Azure 公共 IP 地址。 如果扩展无法访问这些终结点，则脚本可能会成功运行，但不会返回结果。 如果要阻止虚拟机上的流量，则可以使用[服务标记](../../virtual-network/security-overview.md#service-tags)以通过 `AzureChinaCloud` 标记允许流量发往 Azure 公共 IP 地址。

<!--Not Available on ## Run a command-->
<!--Not Available on https://portal.azure.cn-->

## <a name="commands"></a>命令

下表显示了可用于 Windows VM 的命令的列表。 **RunPowerShellScript** 命令可用来运行你需要的任何自定义脚本。

|**名称**|**说明**|
|---|---|
|**RunPowerShellScript**|执行 PowerShell 脚本|
|**EnableRemotePS**|配置计算机以启用远程 PowerShell。|
|**EnableAdminAccount**|检查本地管理员帐户是否被禁用，如果是，则启用它。|
|**IPConfig**| 显示绑定到 TCP/IP 的每个适配器的 IP 地址、子网掩码和默认网关的详细信息。|
|**RDPSettings**|检查注册表设置和域策略设置。 提供策略操作建议（如果计算机属于某个域），或者将设置修改为默认值。|
|**ResetRDPCert**|删除绑定到 RDP 侦听器的 SSL 证书，并将 RDP 侦听器安全性还原为默认值。 如果看到与证书有关的任何问题，请使用此脚本。|
|**SetRDPPort**|为远程桌面连接设置默认的或用户指定的端口号。 为端口的入站访问启用防火墙规则。|

## <a name="powershell"></a>PowerShell

下面是使用 [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 在 Azure VM 上运行 PowerShell 脚本的示例。 该 cmdlet 需要 `-ScriptPath` 参数中引用的脚本位于运行该 cmdlet 的位置本地。

```powershell
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制对“运行命令”的访问

列出“运行命令”或显示某个命令的详细信息需要订阅级别的 `Microsoft.Compute/locations/runCommands/read` 权限，内置的[读者](../../role-based-access-control/built-in-roles.md#reader)角色或更高角色具有此权限。

运行某个命令需要订阅级别的 `Microsoft.Compute/virtualMachines/runCommand/action` 权限，[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高角色具有此权限。

若要使用“运行命令”，可以使用[内置](../../role-based-access-control/built-in-roles.md)角色之一，也可以创建一个[自定义](../../role-based-access-control/custom-roles.md)角色。

## <a name="next-steps"></a>后续步骤

参阅[在 Windows VM 中运行脚本](run-scripts-in-vm.md)来了解远程在 VM 中运行脚本和命令的其他方式。