---
ms.openlocfilehash: cf614480b79a8fa433327d05fed6da4eab1e954e
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632760"
---
## <a name="virtual-network"></a>虚拟网络
虚拟网络 (VNET) 和子网资源可帮助定义 Azure 中运行的工作负载的安全边界。 VNet 的特征包括一个地址空间（定义为 CIDR 块）的集合。 

> [!NOTE]
> 网络管理员应熟悉 CIDR 表示法。 如果不熟悉 CIDR，请[了解其详细信息](http://whatismyipaddress.com/cidr)。
> 
> 

![具有多个子网的 VNet](./media/resource-groups-networking/Figure4.png)

VNet 包含以下属性。

| 属性 | 说明 | 示例值 |
| --- | --- | --- |
| addressSpace |在 CIDR 表示法中构成 VNet 的地址前缀集合 |192.168.0.0/16 |
| **subnets** |构成 VNet 的子网集合 |请参阅下面的[子网](#Subnets)。 |
| ipAddress |分配给对象的 IP 地址。 这是只读属性。 |104.42.233.77 |

### <a name="subnets"></a>子网
子网是 VNet 的子资源，有助于使用 IP 地址前缀在 CIDR 块中定义地址空间的段。 可以将 NIC 添加到子网，并连接到 VM，以便为各种工作负荷提供连接。

子网包含以下属性。 

| 属性 | 说明 | 示例值 |
| --- | --- | --- |
| **addressPrefix** |在 CIDR 表示法中构成子网的单个地址前缀 |192.168.1.0/24 |
| **networkSecurityGroup** |应用到子网的 NSG |请参阅 [NSG](#Network-Security-Group) |
| routeTable |应用到子网的路由表 |请参阅 [UDR](#Route-table) |
| ipConfigurations |连接子网的 NIC 所用的 IP 配置对象集合 |请参阅 [UDR](#Route-table) |

JSON 格式的示例 VNet：

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "chinanorth",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>其他资源
* 获取有关 [VNet](../articles/virtual-network/virtual-networks-overview.md) 的详细信息。
  <!-- URL not Avaialbe in Global * Read the [REST API reference documentation](https://msdn.microsoft.com/library/azure/mt163650.aspx) for VNets. -->
* 阅读子网的 [REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163618.aspx)。