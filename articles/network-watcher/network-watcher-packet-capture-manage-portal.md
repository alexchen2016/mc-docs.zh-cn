---
title: 使用 Azure 网络观察程序管理数据包捕获 - Azure 门户 | Azure
description: 了解如何使用 Azure 门户管理网络观察程序的数据包捕获功能。
services: network-watcher
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 09/10/2018
ms.date: 09/30/2018
ms.author: v-yeche
ms.openlocfilehash: fd73306871d3cc4ee72d2d66d058e7cf265cc42d
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625993"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>在门户中使用 Azure 网络观察程序管理数据包捕获

使用网络观察程序数据包捕获，可以创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻在所需虚拟机上手动运行数据包捕获的负担，从而节省宝贵的时间。

在本文中，你将了解如何启动、停止、下载和删除数据包捕获。 

## <a name="before-you-begin"></a>准备阶段

数据包捕获需要以下连接：
* 通过端口 443 与存储帐户的出站连接。
* 与 169.254.169.254 的入站和出站连接
* 与 168.63.129.16 的入站和出站连接

如果网络安全组关联到网络接口或该网络接口所在的子网，请确保存在允许上述端口的规则。 

## <a name="start-a-packet-capture"></a>启动数据包捕获

1. 在浏览器中，导航到 [Azure 门户](https://portal.azure.cn)，选择“所有服务”，然后在“网络”部分中选择“网络观察程序”。
2. 在“网络诊断工具”下选择“数据包捕获”。 将列出任何现有的数据包捕获，无论其状态如何。
3. 选择“添加”以创建数据包捕获。 可以为以下属性选择值：
   - **订阅**：你要为其创建数据包捕获的虚拟机所在的订阅。
   - **资源组**：虚拟机的资源组。
   - **目标虚拟机**：你要为其创建数据包捕获的虚拟机。
   - **数据包捕获名称**：数据包捕获的名称。
   - **存储帐户或文件**：选择“存储帐户”、“文件”或同时选择两者。 如果选择了“文件”，则捕获将写入到虚拟机中的某个路径。
   - **本地文件路径**：虚拟机上将用来保存数据包捕获的本地路径（仅当选择了“文件”时有效）。 该路径必须是一个有效的路径。 如果使用 Linux 虚拟机，则路径必须以 */var/captures* 开头。
   - **存储帐户**：如果选择了“存储帐户”，请选择一个现有的存储帐户。 仅当选择了“存储”时此选项才可用。

     > [!NOTE]
     > 目前不支持使用高级存储帐户存储数据包捕获。

   - **每个数据包的最大字节数**：捕获的每个数据包中的字节数。 如果留空，将捕获所有字节。
   - **每个会话的最大字节数**：捕获的字节总数。 一旦达到此值，数据包捕获便停止。
   - **时间限制(秒)**：在停止数据包捕获之前的时间限制。 默认为 18,000 秒。
   - 筛选（可选）。 选择“+ 添加筛选器”
     - **协议**：要筛选的数据包捕获协议。 可用值为 TCP、UDP 和 Any。
     - **本地 IP 地址**：在数据包捕获中筛选其中的本地 IP 地址与此值匹配的数据包。
     - **本地端口**：在数据包捕获中筛选其中的本地端口与此值匹配的数据包。
     - **远程 IP 地址**：将数据包捕获筛选为远程 IP 地址与此值匹配的数据包。
     - **远程端口**：在数据包捕获中筛选其中的远程端口与此值匹配的数据包。

     > [!NOTE]
     > 端口和 IP 地址值可以是单个值、值的范围，或某个范围，例如，可为端口指定 80-1024。 可以根据需要定义任意数量的筛选器。

4. 选择“确定” 。

超过为数据包捕获设置的时间限制后，数据包捕获将停止，并且可供查看。 也可以手动停止数据包捕获会话。

> [!NOTE]
> 门户会自动执行以下操作：
>  * 在你选择的虚拟机所在的区域中创建一个网络观察程序（如果该区域尚没有网络观察程序）。
>  * 向虚拟机中添加 *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) 或 [Windows](../virtual-machines/windows/extensions-nwa.md) 虚拟机扩展（如果尚未安装）。

## <a name="delete-a-packet-capture"></a>删除数据包捕获

1. 在数据包捕获视图中，选择数据包捕获右侧的 **...**，或者右键单击现有的数据包捕获，然后选择“删除”。
2. 系统会要求确认是否要删除数据包捕获。 请选择“是”。

> [!NOTE]
> 删除数据包捕获不会删除存储帐户中的或虚拟机上的捕获文件。

## <a name="stop-a-packet-capture"></a>停止数据包捕获

在数据包捕获视图中，选择数据包捕获右侧的 **...**，或者右键单击现有的数据包捕获，然后选择“停止”。

## <a name="download-a-packet-capture"></a>下载数据包捕获

在数据包捕获会话完成后，捕获文件将上传到 Blob 存储或虚拟机上的本地文件。 数据包捕获的存储位置是在创建数据包捕获时定义的。 用于访问保存到存储帐户的捕获文件的便利工具是 Azure 存储资源管理器，可以[下载](http://storageexplorer.com/)该工具。

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.chinacloudapi.cn/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

如果在创建捕获时选择了“文件”，则可以从在虚拟机上配置的路径查看或下载该文件。

## <a name="next-steps"></a>后续步骤

<!--Not Available [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) -->

- 若要确定是否允许特定流量出入虚拟机，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-vm-network-traffic-filtering-problem.md)。

<!--Update_Description: update link, wording update -->

