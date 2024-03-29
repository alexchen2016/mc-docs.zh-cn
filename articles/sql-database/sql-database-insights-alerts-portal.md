---
title: 使用 Azure 门户设置警报和通知 | Microsoft Docs
description: 使用 Azure 门户创建 SQL 数据库警报，该警报可在满足指定的条件时触发通知或自动化操作。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: jrasnik, carlrab
manager: digimobile
origin.date: 11/02/2018
ms.date: 04/08/2019
ms.openlocfilehash: e5ac02407b24e18e4c59a317f5bbe305e6df4e78
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003511"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>使用 Azure 门户为 Azure SQL 数据库和数据仓库创建警报

## <a name="overview"></a>概述
本文介绍如何使用 Azure 门户设置 Azure SQL 数据库和数据仓库警报。 当某些指标（例如数据库大小或 CPU 使用率）达到阈值时，警报可以向你发送电子邮件或调用 Webhook。 本文还提供了设置警报期限的最佳做法。    

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。    
* **活动日志事件** - 警报可以在发生 *每个* 事件时都触发，也可以仅在发生特定数量的事件时触发。

可以配置警报以在其触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 向指定的其他电子邮件地址发送电子邮件。
* 调用 Webhook

可以使用以下工具配置和获取关于警报的信息：

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口 (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则
1. 在此[门户](https://portal.azure.cn/)，查找想要监视的资源并选中它。
2. 在“监视”部分下，选择“警报(经典)”。 对于不同的资源，文本和图标可能会略有不同。  
   
     ![监视](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **仅 SQL DW**：单击“DWU 使用情况”图。 选择“查看经典警报”

3. 选择“添加指标警报(经典)”按钮，并填写字段。
   
    ![添加警报](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **命名**警报规则，并选择也在通知电子邮件中显示的“说明”。
5. 选择想要监视的“指标”为该指标选择一个“条件”和“阈值”。 还选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段"PT5M"，且警报针对 CPU 高于 80% 的情况，则平均 CPU 高于 80% 达到 5 分钟时触发警报。 第一次触发结束后，当平均 CPU 低于 80% 的时间超过 5 分钟时，将再次触发。 每 1 分钟对 CPU 进行一次测量。 请参阅下表，了解支持的时间窗口和每个警报使用的聚合类型（并非所有警报都使用平均值）。   
6. 如果触发警报时希望向管理员和共同管理员发送电子邮件，则选择“向所有者发送电子邮件...”。
7. 触发警报时，如果希望其他电子邮件收到通知，请将其添加到“其他管理员电子邮件”字段下。 用分号隔开多个电子邮件 - *email\@contoso.com;email2\@contoso.com*
8. 触发警报时，如果希望调用有效的 URI，请将其放入“Webhook”字段。
9. 警报创建完成后，选择“确定”。   

在几分钟后，警报将如前所述激活并触发。

## <a name="managing-your-alerts"></a>管理警报
在创建警报后，可以选择警报并且：

* 查看其中显示了指标阈值和前一天的实际值的图形。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。


## <a name="sql-database-alert-values"></a>SQL 数据库警报值

| 资源类型 | 指标名称 | 友好名称 | 聚合类型 | 最小警报时间范围|
| --- | --- | --- | --- | --- |
| SQL 数据库 | cpu_percent | CPU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | log_write_percent | 日志 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_consumption_percent | DTU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | storage | 数据库总大小 | 最大值 | 30 分钟 |
| SQL 数据库 | connection_successful | 成功的连接数 | 总计 | 10 分钟 |
| SQL 数据库 | connection_failed | 失败的连接数 | 总计 | 10 分钟 |
| SQL 数据库 | blocked_by_firewall | 被防火墙阻止 | 总计 | 10 分钟 |
| SQL 数据库 | deadlock | 死锁数 | 总计 | 10 分钟 |
| SQL 数据库 | storage_percent | 数据库大小百分比 | 最大值 | 30 分钟 |
| SQL 数据库 | xtp_storage_percent | In-Memory OLTP 存储百分比（预览） | 平均值 | 5 分钟 |
| SQL 数据库 | workers_percent | 辅助角色百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | sessions_percent | 会话百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_limit | DTU 限制 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_used | 已用的 DTU | 平均值 | 5 分钟 |
||||||
| 弹性池 | cpu_percent | CPU 百分比 | 平均值 | 10 分钟 |
| 弹性池 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 10 分钟 |
| 弹性池 | log_write_percent | 日志 IO 百分比 | 平均值 | 10 分钟 |
| 弹性池 | dtu_consumption_percent | DTU 百分比 | 平均值 | 10 分钟 |
| 弹性池 | storage_percent | 存储百分比 | 平均值 | 10 分钟 |
| 弹性池 | workers_percent | 辅助角色百分比 | 平均值 | 10 分钟 |
| 弹性池 | eDTU_limit | eDTU 限制 | 平均值 | 10 分钟 |
| 弹性池 | storage_limit | 存储限制 | 平均值 | 10 分钟 |
| 弹性池 | eDTU_used | 已用的 eDTU | 平均值 | 10 分钟 |
| 弹性池 | storage_used | 已用的存储量 | 平均值 | 10 分钟 |
||||||               
| SQL 数据仓库 | cpu_percent | CPU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | connection_successful | 成功的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | connection_failed | 失败的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | blocked_by_firewall | 被防火墙阻止 | 总计 | 10 分钟 |
| SQL 数据仓库 | service_level_objective | 数据库服务层 | 总计 | 10 分钟 |
| SQL 数据仓库 | dwu_limit | dwu 限制 | 最大值 | 10 分钟 |
| SQL 数据仓库 | dwu_consumption_percent | DWU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | dwu_used | 已用的 DWU | 平均值 | 10 分钟 |
||||||


## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](../monitoring-and-diagnostics/monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* [大致了解诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)并收集服务的详细高频指标。
* [大致了解指标收集](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
