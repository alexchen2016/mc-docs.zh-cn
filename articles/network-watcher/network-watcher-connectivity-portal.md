---
title: 使用 Azure 网络观察程序排查连接问题 - Azure 门户 | Azure
description: 了解如何通过 Azure 门户使用 Azure 网络观察程序的排查连接问题功能。
services: network-watcher
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 08/03/2017
ms.date: 10/19/2018
ms.author: v-lingwu
ms.openlocfilehash: 9f942a3a9451698b3170107f21409a9dc86b39e0
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52667054"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>通过 Azure 门户使用 Azure 网络观察程序排查连接问题

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用排查连接问题来验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有以下资源：

* 要排查连接问题的区域中的网络观察程序实例。
* 用以排查连接问题的虚拟机。

> [!IMPORTANT]
> 连接故障排除需要从中进行故障排除的 VM 安装了 `AzureNetworkWatcherExtension` VM 扩展。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md?toc=%2fnetwork-watcher%2ftoc.json)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md?toc=%2fnetwork-watcher%2ftoc.json)。 在目标终结点上不需要该扩展。

## <a name="check-connectivity-to-a-virtual-machine"></a>检查与虚拟机的连接

此示例通过端口 80 检查与目标虚拟机的连接。

导航到网络观察程序并单击“排查连接问题”。 选择虚拟机以检查其连接性。 在“目标”部分，选择“选择虚拟机”，并选择正确的虚拟机和端口来进行测试。

单击“检查”后，将检查指定的端口上的虚拟机间的连接性。 在此示例中，目标虚拟机不可访问，并显示了一个跃点列表。

![查看虚拟机的连接性结果][1]

## <a name="check-remote-endpoint-connectivity"></a>检查远程终结点连接性

若要检查远程终结点的连接性和延迟性，请在“目标”区域中选择“手动指定”单选按钮，输入 URL 和端口并单击“检查”。  此步骤适用于网站等远程终结点及存储终结点。

![查看网站的连接性结果][2]

## <a name="next-steps"></a>后续步骤

<!-- Not Available on  [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) --> 访问[查看“IP 流验证”](network-watcher-check-ip-flow-verify-portal.md)，了解是否允许某些流量传入和传出 VM

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png

<!--Update_Description: update link, wording update -->