---
title: 在 Azure 门户中为 Azure Database for PostgreSQL（单一服务器）配置指标警报
description: 本文介绍如何通过 Azure 门户配置和访问针对 Azure Database for PostgreSQL（单一服务器）的指标警报。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 5/6/2019
ms.date: 05/20/2019
ms.openlocfilehash: 135a800dab3cd7520578d835247ab3d45fa0a2f7
ms.sourcegitcommit: 11d81f0e4350a72d296e5664c2e5dc7e5f350926
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65732032"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>使用 Azure 门户设置针对 Azure Database for PostgreSQL（单一服务器）指标的警报

本文介绍如何使用 Azure 门户设置 Azure Database for PostgreSQL 警报。 可根据监视指标接收 Azure 服务的警报。

当指定的指标值越过了分配的阈值时，就会触发此警报。 首次满足条件时，以及之后不再满足条件时，都会触发此警报。 

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同管理员发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook。

可使用以下项配置并获取预警规则相关信息：
* [Azure 门户](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 监视器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建警报规则
1. 在 [Azure 门户](https://portal.azure.cn/)中，选择要监视的 Azure Database for PostgreSQL 服务器。

2. 在边栏的“监视”部分，选择“警报”，如下所示：

   ![选择“警报规则”](./media/howto-alert-on-metric/2-alert-rules.png)

3. 选择“添加警报规则”（+ 图标）。

4. 随即打开“创建规则”页面，如下所示。 填写所需信息：

   ![添加指标警报窗体](./media/howto-alert-on-metric/4-add-rule-form.png)

5. 在“条件”部分中，选择“添加条件”。

6. 从要发出警报的信号列表中选择一个指标。 在此示例中，选择“删除 PostgreSQL 服务器”。
   
   ![选择指标](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. 配置警报逻辑，包括“事件级别”（例如， “警告”）、“状态”（例如， “已启动”）。
   
   完成后选择“完成”。

   ![选择指标](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. 在“操作组”部分中，选择“新建”创建新组以接收有关警报的通知。

9. 使用名称、短名称、订阅和资源组填写“添加操作组”表单。

10. 配置“电子邮件/短信”操作类型。
    
    选择“电子邮件”，键入用于接收通知的电子邮件地址。
   
    （可选）如果希望在警报触发时调用有效的 URI，请将其放入“Webhook”字段。

    完成后选择“确定”。

    ![操作组](./media/howto-alert-on-metric/10-action-group-type.png)

11. 指定警报规则名称、说明和保存警报的资源组。

    ![操作组](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. 选择“创建警报规则”可以创建警报。

    在几分钟后，警报将如前所述激活并触发。

## <a name="manage-your-alerts"></a>管理警报
创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报。

## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* [大致了解指标收集](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
