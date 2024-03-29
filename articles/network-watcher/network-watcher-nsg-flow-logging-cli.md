---
title: 使用 Azure 网络观察程序管理网络安全组流日志 - Azure CLI | Azure
description: 此页说明如何在 Azure 网络观察程序中使用 Azure CLI 管理网络安全组流日志
services: network-watcher
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/22/2017
ms.date: 11/26/2018
ms.author: v-lingwu
ms.openlocfilehash: def40c456058d685e7b744553283f70770aaa1e7
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674252"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>使用 Azure CLI 配置网络安全组流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

网络安全组流日志是网络观察程序的一项功能，可用于查看有关通过网络安全组的入口和出口 IP 流量的信息。 这些流日志以 json 格式编写，并基于每个规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (CLI)](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

> [!NOTE] 
> 流日志版本 2 仅在中国北部区域可用。 配置可通过 Azure 门户和 REST API 获取。 在不支持的区域启用版本 2 日志时，版本 1 日志就会输出到存储帐户中。

## <a name="register-insights-provider"></a>注册 Insights 提供程序

要使流日志记录正常工作，必须注册 **Microsoft.Insights** 提供程序。 如果不确定 **Microsoft.Insights** 提供程序是否已注册，请运行以下脚本。

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>启用网络安全组流日志

以下示例显示了用于启用流日志的命令：

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
```

指定的存储帐户不能配置有仅限 Azure 服务或特定虚拟网络进行网络访问的网络规则。 存储帐户可以与启用流日志的 NSG 使用相同或不同的 Azure 订阅。 如果使用不同的订阅，它们必须都与同一 Azure Active Directory 租户相关联。 用于每个订阅的帐户必须有[必要的权限](required-rbac-permissions.md)。 

如果存储帐户与网络安全组位于不同的资源组或订阅中，请指定存储帐户的完整 ID，而非其名称。 例如，如果存储帐户位于名为 RG-Storage 的资源组中，则请指定 /subscriptions/ {SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName，而不是在上一命令中指定 storageAccountName。

## <a name="disable-network-security-group-flow-logs"></a>禁用网络安全组流日志

使用以下示例禁用流日志：

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>下载流日志

流日志的存储位置是在创建时定义的。 用于访问这些保存到存储帐户的流日志的便利工具是 Azure 存储资源管理器，下载地址为： http://storageexplorer.com/

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.chinacloudapi.cn/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

有关日志结构的信息，请访问[网络安全组流日志概述](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerBI 直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)

了解如何[使用开源工具直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

<!--Update_Description: wording update -->
