---
title: 在 Azure 上的 Linux VM 中创建 shell 脚本
description: 本主题介绍如何使用“运行命令”在 Azure Linux 虚拟机中运行脚本
services: automation
ms.service: automation
author: rockboyfor
ms.author: v-yeche
origin.date: 04/26/2019
ms.date: 05/20/2019
ms.topic: article
manager: digimobile
ms.openlocfilehash: 1038c33659b5c39df49fc50f881670de76f2ac21
ms.sourcegitcommit: 878a2d65e042b466c083d3ede1ab0988916eaa3d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835841"
---
<!--Verify sccessfully-->
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>使用“运行命令”在 Linux VM 中运行 shell 脚本

“运行命令”使用 VM 代理在 Azure Linux VM 中运行 shell 脚本。 这些脚本可以用于常规的计算机或应用程序管理，并且可以用来快速诊断和修正 VM 访问和网络问题并使 VM 恢复正常运行状态。

## <a name="benefits"></a>优点

有多个选项可以用来访问虚拟机。 “运行命令”可以使用 VM 代理在虚拟机上以远程方式运行脚本。 对于 Linux VM，可以通过 Azure 门户、[REST API](https://docs.microsoft.com/rest/api/compute/virtual%20machines%20run%20commands/runcommand) 或 [Azure CLI](https://docs.azure.cn/zh-cn/cli/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 使用“运行命令”。

此功能适用于要在虚拟机中运行脚本的所有方案，并且是排查和修正因网络或管理用户配置错误而未打开 RDP 或 SSH 端口的虚拟机的唯一方法。

## <a name="restrictions"></a>限制

下面是在使用“运行命令”时存在的一系列限制。

* 输出限制为最后的 4096 个字节
* 运行脚本的最短时间大约为 20 秒
* 在 Linux 上，脚本默认情况下以提升用户的身份运行
* 一次只能运行一个脚本
* 不支持提示输入信息（交互模式）的脚本。
* 无法取消正在运行的脚本
* 脚本最多可以运行 90 分钟，之后它将超时
* 需要从 VM 建立出站连接才能返回脚本的结果。

> [!NOTE]
> 若要正常工作，运行命令需要连接（端口 443）到 Azure 公共 IP 地址。 如果扩展无法访问这些终结点，则脚本可能会成功运行，但不会返回结果。 如果要阻止虚拟机上的流量，则可以使用[服务标记](../../virtual-network/security-overview.md#service-tags)以通过 `AzureChinaCloud` 标记允许流量发往 Azure 公共 IP 地址。

## <a name="azure-cli"></a>Azure CLI

下面是使用 [az vm run-command](https://docs.azure.cn/zh-cn/cli/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 命令在 Azure Linux VM 上运行 shell 脚本的示例。

```azurecli
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 若要以另一用户的身份运行命令，可以通过 `sudo -u` 来指定要使用的用户帐户。

<!--Not Available on ## Azure portal-->

## <a name="available-commands"></a>可用命令

下表显示了可用于 Linux VM 的命令的列表。 **RunShellScript** 命令可用来运行所需的任何自定义脚本。

|**名称**|**说明**|
|---|---|
|**RunShellScript**|执行 Linux shell 脚本。|
|**ifconfig**| 获取所有网络接口的配置。|

## <a name="limiting-access-to-run-command"></a>限制对“运行命令”的访问

列出“运行命令”或显示某个命令的详细信息需要订阅级别的 `Microsoft.Compute/locations/runCommands/read` 权限，内置的[读者](../../role-based-access-control/built-in-roles.md#reader)角色或更高角色具有此权限。

运行某个命令需要订阅级别的 `Microsoft.Compute/virtualMachines/runCommand/action` 权限，[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高角色具有此权限。

若要使用“运行命令”，可以使用[内置](../../role-based-access-control/built-in-roles.md)角色之一，也可以创建一个[自定义](../../role-based-access-control/custom-roles.md)角色。

## <a name="next-steps"></a>后续步骤

请参阅[在 Linux VM 中运行脚本](run-scripts-in-vm.md)，了解以远程方式在 VM 中运行脚本和命令的其他方式。

<!--Update_Description: new articles on linux run command -->
<!--ms.date: 05/20/2019-->