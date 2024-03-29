---
title: 为 Azure 网络接口配置 IP 地址 | Azure
description: 了解如何为网络接口添加、更改和删除专用和公共 IP 地址。
services: virtual-network
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 07/24/2017
ms.date: 06/10/2019
ms.author: v-yeche
ms.openlocfilehash: a5284ab1d9646224e2da4fc86a04d5aba837eae9
ms.sourcegitcommit: df1b896faaa87af1d7b1f06f1c04d036d5259cc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66250457"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>为 Azure 网络接口添加、更改或删除 IP 地址

了解如何为网络接口添加、更改和删除网公共和专用 IP 地址。 通过分配给网络接口的专用 IP 地址，虚拟机能够与 Azure 虚拟网络和所连接的网络中的其他资源进行通信。 通过专用 IP 地址，还能够使用不可预测的 IP 地址实现到 Internet 的出站通信。 通过分配给网络接口的[公共 IP 地址](virtual-network-public-ip-address.md)，可以实现从 Internet 到虚拟机的入站通信。 通过此地址，还能够使用不可预测的 IP 地址实现从虚拟机到 Internet 的出站通信。 有关详细信息，请参阅[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fvirtual-network%2ftoc.json)。

如果需要创建、更改或删除网络接口，请阅读[管理网络接口](virtual-network-network-interface.md)一文。 如果需要向虚拟机添加网络接口或从中删除网络接口，请阅读[添加或删除网络接口](virtual-network-network-interface-vm.md)一文。

<a name="before"></a>
## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。
- 如果使用门户，请打开 https://portal.azure.cn，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请从计算机运行 PowerShell。  本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount -Environment AzureChinaCloud` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行界面 (CLI) 命令来完成本文中的任务，请从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

必须将登录或连接到 Azure 所用的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有“[网络接口权限](virtual-network-network-interface.md#permissions)”中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fvirtual-network%2ftoc.json)。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

<a name="create-ip-config"></a>
## <a name="add-ip-addresses"></a>添加 IP 地址

可将所需的任意数量的[专用](#private)和[公共](#public) [IPv4](#ipv4) 地址添加到网络接口，只要不超过 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文中列出的限制即可。

<!-- Not Avaiable on IPV6 -->

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。   当“网络接口”出现在搜索结果中时，请选择它。 
2. 从列表中选择要为其添加 IPv4 地址的网络接口。
3. 在“设置”  下选择“IP 配置”  。
4. 在“IP 配置”  下选择“+ 添加”  。
5. 指定以下内容，然后选择“确定”  ：

    |设置|必需？|详细信息|
    |---|---|---|
    |Name|是|对于网络接口必须是唯一的|
    |类型|是|由于要将 IP 配置添加到现有网络接口，并且每个网络接口都必须有一个[主要](#primary) IP 配置，因此，你的唯一选项是“辅助”。 |
    |专用 IP 地址分配方法|是|[**动态**](#dynamic)：Azure 为在其中部署网络接口的子网地址范围分配下一可用地址。 [**静态**](#static)：为在其中部署网络接口的子网地址范围分配未使用的地址。|
    |公共 IP 地址|否|**禁用：** 当前没有公共 IP 地址资源关联到 IP 配置。 **启用：** 选择现有的 IPv4 公共 IP 地址，或新建一个。 若要了解如何创建公共 IP 地址，请参阅[公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)一文。|
6. 可以遵循[将多个 IP 地址分配到虚拟机操作系统](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的说明，手动将辅助专用 IP 地址添加到虚拟机操作系统。 在手动向虚拟机操作系统添加 IP 地址之前，请参阅[专用](#private) IP 地址以了解特殊注意事项。 请不要向虚拟机操作系统添加任何公共 IP 地址。

 命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config create](https://docs.azure.cn/zh-cn/cli/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

<a name="change-ip-config"></a>
## <a name="change-ip-address-settings"></a>更改 IP 地址设置

你可能需要更改 IPv4 地址的分配方法、更改静态 IPv4 地址，或者更改分配给网络接口的公共 IP 地址。 如果要更改与虚拟机中的辅助网络接口关联的辅助 IP 配置的专用 IPv4 地址（有关详细信息，请参阅[主要网络接口和辅助网络接口](virtual-network-network-interface-vm.md)），请将该虚拟机置于“已停止”（“已解除分配”）状态，并完成以下步骤：

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。   当“网络接口”出现在搜索结果中时，请选择它。 
2. 从列表中选择要查看或更改 IP 网络设置的网络接口。
3. 在“设置”  下选择“IP 配置”  。
4. 从列表中选择想要修改的 IP 配置。
5. 使用有关[添加 IP 配置](#add-ip-addresses)的步骤 5 中的设置的信息，根据需要更改设置。
6. 选择“其他安全性验证”  。

>[!NOTE]
>在 Windows 中，如果主要网络接口有多个 IP 配置，并且主要 IP 配置的专用 IP 地址已更改，则必须手动将主要 IP 地址和辅助 IP 地址重新分配给网络接口（在 Linux 中不需要执行此操作）。 若要手动向操作系统中的网络接口分配 IP 地址，请参阅[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)。 有关在手动向虚拟机操作系统添加 IP 地址的特别注意事项，请参阅[专用](#private) IP 地址。 请不要向虚拟机操作系统添加任何公共 IP 地址。

 命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](https://docs.azure.cn/zh-cn/cli/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

<a name="delete-ip-config"></a>
## <a name="remove-ip-addresses"></a>删除 IP 地址

可以从网络接口中删除[专用](#private)和[公共](#public) IP 地址，但网络接口必须始终至少有一个分配给它的专用 IPv4 地址。

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。   当“网络接口”出现在搜索结果中时，请选择它。 
2. 从列表中选择要移除 IP 地址的网络接口。
3. 在“设置”  下选择“IP 配置”  。
4. 右键单击以选择要删除的[辅助](#secondary) IP 配置（无法删除[主要](#primary)配置），单击“删除”，然后选择“是”确认删除   。 如果该配置关联了公共 IP 地址资源，则该资源将从 IP 配置中分离，但不会被删除。

 命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config delete](https://docs.azure.cn/zh-cn/cli/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-delete)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP 配置

[专用](#private)和（可选）[公共](#public) IP 地址分配给一个或多个分配给网络接口的 IP 配置。 有两种 IP 配置：

### <a name="primary"></a>主要

每个网络接口都分配有一个主要 IP 配置。 主要 IP 配置：

- 具有分配给它的[专用](#private) [IPv4](#ipv4) 地址。

    <!-- Not Available on IPv6 -->

- 还可能具有分配给它的[公共](#public) IPv4 地址。 

    <!-- Not Available on IPv6 -->

### <a name="secondary"></a>次要

除了主要 IP 配置之外，网络接口还可能具有零个或多个分配给它的辅助 IP 配置。 辅助 IP 配置：

- 必须具有分配给它的专用 IPv4 地址。 如果地址是 IPv4，则网络接口可以具有多个分配给它的辅助 IP 配置。 若要详细了解可以向网络接口分配多少专用和公共 IPv4 地址，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。  
- 如果专用 IP 地址是 IPv4，则还可以具有分配给它的多个公共 IPv4 地址。 在下列方案中，向网络接口分配多个 IP 地址很有帮助：
    - 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
    - 虚拟机充当网络虚拟设备，例如防火墙或负载均衡器。
    - 可将任何网络接口的任何专用 IPv4 地址添加到 Azure 负载均衡器后端池。 过去，只能将主要网络接口的主要 IPv4 地址添加到后端池。 若要详细了解如何对多个 IPv4 配置进行负载均衡，请参阅[对多个 IP 配置进行负载均衡](../load-balancer/load-balancer-multiple-ip.md?toc=%2fvirtual-network%2ftoc.json)一文。 

        <!-- Not Available on IPv6 -->

## <a name="address-types"></a>地址类型

可以向 [IP 配置](#ip-configurations)分配以下类型的 IP 地址：

### <a name="private"></a>专用

通过专用 [IPv4](#ipv4) 地址，虚拟机能够与虚拟网络或所连接的网络中的其他资源进行通信。

<!-- Not Avaiable on IPV6 -->

默认情况下，Azure DHCP 服务器将 Azure 网络接口的[主要 IP 配置](#primary)的专用 IPv4 地址分配给虚拟机操作系统内的网络接口。 除非必要，永远不应当在虚拟机操作系统中手动设置网络接口的 IP 地址。

> [!WARNING]
> 如果在虚拟机操作系统中设置为网络接口的主要 IP 地址的 IPv4 地址曾经不同于为附加到 Azure 中的虚拟机的主要网络接口的主要 IP 配置分配的专用 IPv4 地址，则会失去到虚拟机的连接。

在许多方案中，需要手动设置虚拟机操作系统内的网络接口的 IP 地址。 例如，在向 Azure 虚拟机添加多个 IP 地址时，必须手动设置 Windows 操作系统的主要和辅助 IP 地址。 对于 Linux 虚拟机，可能只需要手动设置辅助 IP 地址。 有关详细信息，请参阅[向 VM 操作系统添加 IP 地址](virtual-network-multiple-ip-addresses-portal.md#os-config)。 如果需要更改分配给 IP 配置的地址，我们建议：

1. 确保虚拟机接收来自 Azure DHCP 服务器的地址。 之后，将分配的 IP 地址更改回到操作系统中的 DHCP，并重新启动虚拟机。
2. 关闭（解除分配）虚拟机。
3. 在 Azure 中更改 IP 配置的 IP 地址。
4. 启动虚拟机。
5. 在操作系统中[手动配置](virtual-network-multiple-ip-addresses-portal.md#os-config)辅助 IP 地址（在 Windows 内还需要配置主要 IP 地址）以配置在 Azure.

通过遵循上述步骤，在 Azure 中分配给网络接口的专用 IP 地址与在虚拟机操作系统中分配的地址会保持相同。 为了跟踪在操作系统中为订阅中的哪些虚拟机手动设置了 IP 地址，请考虑向虚拟机添加一个 Azure [标记](../azure-resource-manager/resource-group-using-tags.md)。 例如，可以使用“IP 地址分配: 静态”。 这样，可以轻松找到订阅中在操作系统中手动为其设置了 IP 地址的虚拟机。

通过专用 IP 地址，虚拟机除了能够与同一网络中的或所连接的虚拟网络中的其他资源进行通信外，还能够进行到 Internet 的出站通信。 出站连接是由 Azure 转换为不可预测的公共 IP 地址的源网络地址。 若要了解 Azure 出站 Internet 连接的详细信息，请阅读 [Azure 出站 Internet 连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fvirtual-network%2ftoc.json)一文。 不能从 Internet 进行到虚拟机的专用 IP 地址的入站通信。 如果出站连接需要可预测的公共 IP 地址，则将公共 IP 地址资源关联到网络接口。

### <a name="public"></a>公共

通过公共 IP 地址资源分配的公共 IP 地址允许以入站连接的方式从 Internet 连接到虚拟机。 到 Internet 的出站连接使用不可预测的 IP 地址。 有关详细信息，请参阅[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fvirtual-network%2ftoc.json)。 可以为 IP 配置分配公共 IP 地址，但这不是必需的。 如果没有通过关联公共 IP 地址资源的方式向虚拟机分配公共 IP 地址，则虚拟机仍可以出站方式与 Internet 通信。 在这种情况下，专用 IP 地址是由 Azure 转换为不可预测的公共 IP 地址的源网络地址。 若要了解有关公共 IP 地址资源的详细信息，请参阅[公共 IP 地址资源](virtual-network-public-ip-address.md)。

可分配给网络接口的专用和公共 IP 地址数有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

> [!NOTE]
> Azure 会将虚拟机的专用 IP 地址转换为公共 IP 地址。 因此，虚拟机的操作系统不会意识到分配给它的任何公共 IP 地址，因此不需要在操作系统中手动分配公共 IP 地址。

## <a name="assignment-methods"></a>分配方法

公共和专用 IP 地址是使用以下分配方法之一分配的：

### <a name="dynamic"></a>动态

默认情况下会分配动态专用 IPv4 地址。 

- **仅公共**：Azure 从特定于每个 Azure 区域的范围分配地址。 若要了解向每个区域分配了哪些范围，请参阅 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=57062)。 如果在停止（解除分配）虚拟机后又重新启动，则地址可能会更改。 

    <!-- Not Available on IPv6 -->

- **仅专用**：Azure 保留每个子网地址范围中的前四个地址，不分配这些地址。 Azure 将下一个可用的地址分配给子网地址范围中的资源。 例如，如果子网的地址范围为 10.0.0.0/16，且地址 10.0.0.0.4-10.0.0.14 已分配（.0-.3 为保留地址），则 Azure 会将 10.0.0.15 分配给资源。 动态方法是默认的分配方法。 动态 IP 地址在分配后，仅在以下情况下才会释放：网络接口已删除、已分配到同一虚拟网络中的另一子网，或者分配方法已更改为静态，这种情况下会指定另一 IP 地址。 默认情况下，当分配方法从动态更改为静态时，Azure 会将以前的动态分配的地址作为静态地址分配。

    <!-- Not Available on IPv6 -->

### <a name="static"></a>静态

（可选）可为 IP 配置分配公共或专用静态 IPv4 地址。 若要详细了解 Azure 如何分配静态公用 IPv4 地址，请参阅[公用 IP 地址](virtual-network-public-ip-address.md)。

<!-- Not Available on IPv6 -->

- **仅公共**：Azure 从特定于每个 Azure 区域的范围分配地址。 可以下载 Azure [中国](https://www.microsoft.com/download/confirmation.aspx?id=57062)云的范围（前缀）列表。  该地址不会更改，除非其所分配到的公共 IP 地址资源已删除，或者分配方法已更改为动态。 如果将公共 IP 地址资源关联到某个 IP 配置，则必须取消其与 IP 配置的关联，然后才能更改其分配方法。
- **仅专用**：由你选择并分配子网地址范围中的地址。 分配的地址可以是子网地址范围中的任何地址，前提是该地址不是子网地址范围中的头四个地址，也不是当前已分配给子网中任何其他资源的地址。 只有在删除网络接口之后，静态地址才会释放。 如果将分配方法更改为静态，Azure 会动态地将以前分配的静态 IP 地址作为动态地址分配，即使该地址不是子网地址范围内的下一个可用地址。 如果将网络接口分配给同一虚拟网络中的另一子网，则该地址也会更改。但是，若要将网络接口分配给另一子网，必须先将分配方法从静态更改为动态。 将网络接口分配给另一子网以后，即可将分配方法改回为静态，并根据新子网的地址范围分配 IP 地址。

## <a name="ip-address-versions"></a>IP 地址版本

在分配地址时可以指定以下版本：

### <a name="ipv4"></a>IPv4

每个网络接口必须有一个具有分配的[专用](#private) [IPv4](#ipv4) 地址的[主要](#primary) IP 配置。 可以添加一个或多个[辅助](#secondary) IP 配置，每个配置都具有一个 IPv4 专用地址（可选）和一个 IPv4 [公共](#public) IP 地址。

<!-- Not Avaialbel ### IPv6-->

## <a name="skus"></a>SKU

公共 IP 地址是使用基本或标准 SKU 创建的。 有关 SKU 差异的详细信息，请参阅[管理公共 IP 地址](virtual-network-public-ip-address.md)。

> [!NOTE]
> 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](security-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。

## <a name="next-steps"></a>后续步骤
若要创建具有不同 IP 配置的虚拟机，请阅读以下文章：

|任务|工具|
|---|---|
|创建具有多个网络接口的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fvirtual-network%2ftoc.json)|
|创建具有多个 IPv4 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

<!-- Not Avaiable on IPV6 -->
<!--Update_Description: update link, wording update -->
