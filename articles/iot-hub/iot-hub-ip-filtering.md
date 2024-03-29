---
title: Azure IoT 中心 IP 连接筛选器 | Azure
description: 如何使用 IP 筛选阻止特定 IP 地址到 Azure IoT 中心的连接。 可阻止来自单独 IP 地址或 IP 地址范围的连接。
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 05/23/2017
ms.author: v-yiso
ms.date: 03/18/2019
ms.openlocfilehash: adfe63d5306384a17800c11c614675136ed5ad7f
ms.sourcegitcommit: 0582c93925fb82aaa38737a621f04941e7f9c6c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57560468"
---
# <a name="use-ip-filters"></a>使用 IP 筛选器

安全性对于基于 Azure IoT 中心的任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 *IP 筛选器*功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

## <a name="when-to-use"></a>何时使用

对于需要阻止特定 IP 地址的 IoT 中心终结点的情况，有两个具体用例：

- IoT 中心应仅从指定范围内的 IP 地址接收流量并拒绝任何其他流量。 例如，将 IoT 中心与 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) 配合使用，以在 IoT 中心与本地基础结构之间创建专用连接。
- 需要拒绝来自 IoT 中心管理员已标识为可疑地址的 IP 地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

在 IoT 中心服务级别应用 IP 筛选器规则。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。

与 IoT 中心的拒绝 IP 规则匹配的 IP 地址发出的任何连接尝试都会收到“未授权”401 状态代码和说明。 响应消息不提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，门户中针对 IoT 中心的“IP 筛选器”网格为空。 此默认设置意味着中心会接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

![IoT 中心默认 IP 筛选器设置](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

添加 IP 筛选器规则时，系统会提示用户提供以下值：

- **IP 筛选器规则名称** ，必须是一个唯一的、不区分大小写的字母数字字符串，最长 128 个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。
- 选择“拒绝”或“接受”作为 IP 筛选器规则的“操作”。
- 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

![向 IoT 中心添加 IP 筛选规则](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

保存规则后，会出现一个提醒，通知你更新正在进行。

![关于保存 IP 筛选规则的通知](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

当存在的 IP 筛选规则达到最大数目 10 时，“添加”选项被禁用。

可以通过双击包含某个现有规则的行编辑该规则。

> [!NOTE]
> 拒绝 IP 地址即可阻止其他 Azure 服务（例如门户中的 Azure 流分析、Azure 虚拟机或设备资源管理器）与 IoT 中心交互。

> [!WARNING]
> 如果使用 Azure 流分析 (ASA) 并启用 IP 筛选从 IoT 中心读取消息，请在 ASA 连接字符串中使用与事件中心兼容的名称和 IoT 中心的终结点。

## <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

如果要删除 IP 筛选器规则，请在网格中选择一个或多个规则，并单击“删除”。

![删除 IoT 中心 IP 筛选规则](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 检索和更新 IP 筛选器

可以通过 [Azure CLI](/cli/?view=azure-cli-latest) 检索和更新 IoT 中心的 IP 筛选器。 

若要检索 IoT 中心的当前 IP 筛选器，请运行：

```azurecli
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

这会返回一个 JSON 对象，其中会在 `properties.ipFilterRules` 键下列出现有 IP 筛选器：

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

若要为 IoT 中心添加新 IP 筛选器，请运行：

```azurecli
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

若要在 IoT 中心删除现有 IP 筛选器，请运行：

```azurecli
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

请注意，`<ipFilterIndexToRemove>` 必须对应于 IoT 中心 `properties.ipFilterRules` 中的 IP 筛选器顺序。


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 检索和更新 IP 筛选器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以通过 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azps-1.2.0) 检索和设置 IoT 中心的 IP 筛选器。 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 筛选器

还可以使用 Azure 资源提供程序的 REST 终结点检索和修改 IoT 中心的 IP 筛选器。 请参阅 [createorupdate 方法](https://docs.microsoft.com/en-us/rest/api/iothub/iothubresource/createorupdate) `properties.ipFilterRules`。


## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则按顺序应用，与 IP 地址匹配的第一条规则决定了是采取接受操作还是拒绝操作。

例如，若要接受 192.168.100.0/22 范围内的地址并拒绝所有其他地址，则网格中的第一条规则应接受 192.168.100.0/22 这一地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。

![更改 IoT 中心 IP 筛选规则的顺序](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅：

* [操作监视](iot-hub-operations-monitoring.md)
* [IoT 中心指标](iot-hub-metrics.md)