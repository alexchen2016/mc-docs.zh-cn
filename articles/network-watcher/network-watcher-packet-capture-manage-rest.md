---
title: 使用 Azure 网络观察程序管理数据包捕获 - REST API | Azure
description: 此页说明如何使用 Azure REST API 管理网络观察程序的数据包捕获功能
services: network-watcher
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: 53fe0324-835f-4005-afc8-145eeb314aeb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/19
ms.author: v-lingwu
ms.openlocfilehash: 04a7a3b622e22ad872380f222ee7a1198e9a6eef
ms.sourcegitcommit: 0500a329025e55e4e0bdbf68d48d8bcbfab06ec1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597994"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>通过 Azure REST API 使用 Azure 网络观察程序管理数据包捕获

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

使用网络观察程序数据包捕获，可以创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻手动运行数据包捕获的负担，并可在所需计算机上运行，从而可节省宝贵的时间。

本文将引导完成当前可用于数据包捕获的不同管理任务。

- [**获取数据包捕获**](#get-a-packet-capture)
- [**列出所有数据包捕获**](#list-all-packet-captures)
- [**查询数据包捕获的状态**](#query-packet-capture-status)
- [**启动数据包捕获**](#start-packet-capture)
- [**停止数据包捕获**](#stop-packet-capture)
- [**删除数据包捕获**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>准备阶段

本方案通过调用网络观察程序 Rest API 来运行“IP 流验证”。 通过 PowerShell 调用 REST API 时，使用的是 ARMclient。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient

本方案假设已根据[创建网络观察程序](network-watcher-create.md)中的步骤创建了网络观察程序。

> 数据包捕获需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

```powershell
$env:ARMCLIENT_ENV="MOONCAKE"
 armclient login
```

## <a name="retrieve-a-virtual-machine"></a>检索虚拟机

运行以下脚本以返回虚拟机。 以下信息是启动数据包捕获所必需的。

以下代码需要以下变量：

- **subscriptionId** - 还可以使用 **Get-AzSubscription** cmdlet 检索订阅 ID。
- **resourceGroupName** - 包含虚拟机的资源组的名称。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

下一示例使用了以下输出中虚拟机的 ID。

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "chinaeast",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-a-packet-capture"></a>获取数据包捕获

以下示例获取单个数据包捕获的状态

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
armclient post "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

以下响应是在查询数据包捕获状态时返回的典型响应的示例。

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_chinaeast/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_chinaeast/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>列出所有数据包捕获

以下示例获取一个区域中的所有数据包捕获会话。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
armclient get "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

以下响应是在获取所有数据包捕获时返回的典型响应的示例

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_chinaeast/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.chinacloudapi.cn/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_chinaeast/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.chinacloudapi.cn/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>查询数据包捕获状态

以下示例获取一个区域中的所有数据包捕获会话。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

以下响应是在查询数据包捕获状态时返回的典型响应的示例。

```json
{
    "name": "vm1PacketCapture",     "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatche rName}/packetCaptures/{packetCaptureName}",
   "captureStartTime" : "9/7/2016 12:35:24PM",
   "packetCaptureStatus" : "Stopped",
   "stopReason" : "TimeExceeded"
   "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>启动数据包捕获

以下示例在虚拟机上创建一个数据包捕获。  我们已将该示例参数化，方便用户灵活地创建示例。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" # Example: "https://mytestaccountname.blob.core.chinacloudapi.cn/capture/vm1Capture.cap"
$storagePath = ""
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>停止数据包捕获

以下示例在虚拟机上停止一个数据包捕获。  我们已将该示例参数化，方便用户灵活地创建示例。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>删除数据包捕获

以下示例在虚拟机上删除一个数据包捕获。  我们已将该示例参数化，方便用户灵活地创建示例。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_chinaeast"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.chinacloudapi.cn/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> 删除数据包捕获不会删除存储帐户中的文件

## <a name="next-steps"></a>后续步骤

有关从 Azure 存储帐户下载文件的说明，请参阅[通过 .NET 开始使用 Azure Blob 存储](../storage/blobs/storage-dotnet-how-to-use-blobs.md)。 可以使用的另一个工具是存储资源管理器。 有关存储资源管理器的详细信息可以在此链接中找到：[存储资源管理器](http://storageexplorer.com/)

<!--Not Available on [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) -->

<!--Update_Description: update link, wording update -->