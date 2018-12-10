---
title: 使用 Azure Monitor 创建、查看和管理经典指标警报
description: 了解如何使用 Azure 门户、CLI 或 Powershell 来创建、查看和管理经典指标警报规则。
author: lingliw
ms.service: azure-monitor
ms.topic: conceptual
origin.date: 09/18/2018
ms.date: 11/26/2018
ms.author: v-lingwu
ms.component: alerts
ms.openlocfilehash: 25a8762f536ef23c457b1a901697a2be1f5d0236
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675117"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理经典指标警报

Azure Monitor 中的经典指标警报提供了一种在指标超出阈值时获得通知的方法。 经典指标警报是一个较旧的功能，它仅可针对非维度指标发出警报。 有一个称为“指标警报”的较新功能，它具有比经典指标警报改进的功能。 可以在[指标警报概述](alert-metric-overview.md)中了解新的指标警报功能的详细信息。 在本文中，我们将介绍如何通过 Azure 门户、Azure CLI 和 Powershell 创建、查看和管理经典指标警报规则。

## <a name="with-azure-portal"></a>使用 Azure 门户

1. 在[门户](https://portal.azure.cn/)中，找到要监视的资源，然后选择它。

2. 在“监视”部分中，选择“警报(经典)”。 对于不同的资源，文本和图标可能会略有不同。 如果在此处未找到“警报(经典)”，则它可能位于“警报”或“警报规则”中。

    ![监视](media/alert-metric-classic/AlertRulesButton.png)

3. 选择“添加指标警报(经典)”命令，并填写字段。

    ![添加警报](media/alert-metric-classic/AddAlertOnlyParamsPage.png)

4. **命名**警报规则。 然后选择也在通知电子邮件中显示的“说明”。

5. 选择要监视的指标。 然后为该指标选择“条件”和“阈值”值。 还选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段“过去 5 分钟”，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时将触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时会再次触发警报。 CPU 指标度量每分钟进行一次。

6. 如果希望在触发警报时管理员和共同管理员收到电子邮件通知，则选择“向所有者发送电子邮件...”。

7. 如果希望在触发警报时向其他电子邮件发送通知，请将其添加到“其他管理员电子邮件”字段。 使用分号分隔多个电子邮件，格式如下：email@contoso.com; email2@contoso.com

8. 如果希望在触发警报时调用有效的 URI，请将其放入“Webhook”字段中。

9. 如果使用 Azure 自动化时，则可选择在触发警报时要运行的 Runbook。

10. 选择“确定”以创建警报。

在几分钟后，警报将如前所述激活并触发。

创建警报后，可以选择它并执行以下任务之一：

* 查看显示指标阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。

## <a name="with-azure-cli"></a>使用 Azure CLI

前面几个部分介绍了如何使用 Azure 门户创建、查看和管理指标警报规则。 本部分将介绍如何使用跨平台 [Azure CLI](https://docs.azure.cn/zh-cn/cli/get-started-with-azure-cli?view=azure-cli-latest) 实现相同的结果。 开始使用 Azure CLI 的最快方式是通过 [Azure Cloud Shell](/cloud-shell/overview?view=azure-cli-latest)。

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>获取某个资源组中的所有经典指标警报规则

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>查看特定经典指标警报规则的详细信息

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>创建经典指标警报规则

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>删除经典指标警报规则

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>使用 PowerShell

本部分展示了如何使用 PowerShell 命令创建、查看和管理经典指标警报。本文中的示例展示了如何将 Azure Monitor cmdlet 用于经典指标警报。

1. 如果尚未安装，请在计算机上安装要运行的 PowerShell。 有关详细信息，请参阅[如何安装和配置 PowerShell](https://docs.microsoft.com/powershell/azure/overview)。 还可以在 [Azure Monitor (Insights) Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.insights) 上查看 Azure Monitor PowerShell cmdlet 的完整列表。

2. 首先，登录到 Azure 订阅。

    ```PowerShell
    Connect-AzureRmAccount -Environment AzureChinaCloud
    ```

3. 会出现登录界面。 登录帐户后，会出现 TenantID 和默认订阅 ID。 所有 Azure cmdlet 都可用于默认订阅的上下文。 若要查看有权访问的订阅的列表，请使用以下命令：

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. 若要将工作环境更改为另一订阅，请使用以下命令：

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. 可以检索某个资源组中的所有经典指标警报规则：

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. 可以查看经典指标警报规则的详细信息

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. 可以检索为目标资源设置的所有警报规则。 例如，虚拟机上的所有警报规则设置。

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. 可使用 `Add-AlertRule` cmdlet 来创建、更新或禁用警报规则。 可以分别使用 `New-AzureRmAlertRuleEmail` 和 `New-AzureRmAlertRuleWebhook` 创建电子邮件和 webhook 属性。 在警报规则 cmdlet 中，将这些属性作为操作分配给警报规则的“操作”属性。 下表描述了用于使用指标创建警报的参数和值。

    | 参数 | value |
    | --- | --- |
    | Name |simpletestdiskwrite |
    | 此警报规则的位置 |中国东部 |
    | resourceGroup |montest |
    | TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
    | 创建的警报的 MetricName |\PhysicalDisk(_Total)\Disk Writes/sec。有关如何检索精确指标名称的信息，请参阅 `Get-MetricDefinitions` cmdlet |
    | operator |GreaterThan |
    | 阈值（此指标的计数/秒） |1 |
    | WindowSize（hh:mm:ss 格式） |00:05:00 |
    | 聚合（在这种情况下使用平均计数的指标的统计信息） |平均值 |
    | 自定义电子邮件（字符串数组） |'foo@example.com','bar@example.com' |
    | 向所有者、参与者和读者发送电子邮件 |-SendToServiceOwners |

9. 创建电子邮件操作

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    ```

10. 创建 Webhook 操作

    ```PowerShell
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
    ```

## <a name="next-steps"></a>后续步骤

- [使用资源管理器模板创建经典指标警报](monitoring-enable-alerts-using-template.md)。
- [使用 Webhook 让经典指标警报通知非 Azure 系统](insights-webhooks-alerts.md)。