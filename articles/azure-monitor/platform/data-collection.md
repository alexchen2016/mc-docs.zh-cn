---
title: Azure Monitor 收集的监视数据 | Microsoft Docs
description: Azure Monitor 收集的监视数据划分为指标（轻型数据，能够支持近实时方案）和日志（用于高级分析）。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: 5adad8d5c8d5470267527f915a916dd820aa9af1
ms.sourcegitcommit: 6c2fccfec948d2d7906d9b23374331f6acf7090c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204970"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Azure Monitor 收集的监视数据
[Azure Monitor](../overview.md) 是可以帮助你监视应用程序及其依赖的资源的服务。 存储来自受监视源的遥测数据和其他数据是此功能的核心所在。 本文提供有关 Azure Monitor 如何存储和使用此类数据的完整说明。

Azure Monitor 收集的所有数据属于以下两种基本类型之一：[指标](#metrics)和[日志](#logs)。 指标是数字值，用于描述系统某些方面在特定时间点的情况。 指标是轻型数据，可以支持近实时方案。 日志包含不同类型的已经整理成记录的数据，每种类型都有不同的属性集。 与性能数据一样，事件和跟踪等遥测数据也作为日志存储，因此，可将它们合并以进行分析。

![Azure Monitor 概述](media/data-collection/overview.png)

## <a name="metrics"></a>指标
指标是数字值，用于描述系统某些方面在特定时间的情况。 指标是轻型数据，可以支持近实时方案。 不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。

例如，可以每分钟从虚拟机收集一次处理器利用率，也可以每 10 分钟收集一次登录到应用程序的用户数。 当其中某个收集的值，甚至是两个值之间的差超过定义的阈值时，可以激发警报。

Azure 中指标的特定特性包括：

* 按一分钟频率收集（除非指标定义中另有规定）。
* 由指标名称和充当类别的命名空间唯一标识。
* 存储 93 天。 可将指标复制到日志以了解长期趋势。

每个指标值具有以下属性：
* 收集值的时间。
* 值表示的度量类型。
* 与值关联的资源。
* 值本身。
* 某些指标可能具有下一部分所述的多个维度。 自定义指标最多可以包含 10 个维度。

### <a name="multi-dimensional-metrics"></a>多维指标
指标维度是携带附加数据来描述指标值的名称/值对。 例如，指标“可用磁盘空间”可能包含名为“驱动器”并具有值“C:”和“D:”的维度，通过此维度可以查看所有驱动器的可用磁盘空间，或者可以分别查看每个驱动器的可用磁盘空间。

以下示例演示了名为“网络吞吐量”的假设指标的两个数据集。 第一个数据集不包含维度。 第二个数据集使用两个维度（_IP 地址_和_方向_）显示值：

### <a name="network-throughput"></a>网络吞吐量

| Timestamp     | 指标值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

上述不包含维度的指标只能够回答类似“在某个给定时间我的网络吞吐量是多少？”的基本问题

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>网络吞吐量 + 两个维度（“IP 地址”和“方向”）

| Timestamp     | “IP”维度   | “方向”维度 | 指标值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100.1 Kbps |

此指标可以回答类似“每个 IP 地址的网络吞吐量是多少？”，以及“相对于收到的数据，发送的数据有多少？”的问题 与不包含维度的指标相比，多维指标具有更多分析值和诊断值。

### <a name="value-of-metrics"></a>指标值
通常情况下，单个指标本身提供的信息很少， 其提供的单个值没有任何上下文，仅仅是与某个简单的阈值进行比较。 但是，如果能够与其他用于确定模式和趋势的指标组合使用，或者与提供特定值上下文的日志组合使用，则单个指标会很有用。

例如，不能通过应用程序在给定时间的具体用户数来判断应用程序的运行状况。 但是，如果同一指标的多个值指示用户数下降，则说明可能存在问题。 如果应用程序引发的异常过多，而且某个单独的指标也指示存在这种情况，则可能说明是应用程序问题导致用户数下降。 应用程序为识别其组件中的故障而创建的事件可能有助于识别根本原因。

### <a name="sources-of-metric-data"></a>指标数据的源
Azure Monitor 从三个基本源收集指标。 所有这些指标将在指标存储中提供。在指标存储中，不管指标的源是什么，都可以统一评估这些指标。

**平台指标**由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](../../azure-monitor/platform/metrics-supported.md)，无需进行任何配置。

**应用程序指标**由 Application Insights 为受监视的应用程序创建，可帮助检测性能问题，以及跟踪应用程序的用法趋势。 此类指标包括“服务器响应时间”和“浏览器异常”等值。

**自定义指标**是在自动提供的标准指标之外定义的指标。 只能针对该资源所在的同一区域中的单个资源创建自定义指标。 可使用以下方法创建自定义指标：
- 使用 [Windows 诊断扩展 (WAD)](../../azure-monitor/platform/diagnostics-extension-overview.md) 从 Windows 虚拟机发布自定义指标。
- 使用 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)从 Linux 虚拟机发布自定义指标。
- 使用自定义指标 API 在 Azure 服务中编写自定义指标。

![指标概述](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>指标有哪些作用？
可以使用指标执行的任务包括：

- 使用[指标资源管理器](../../azure-monitor/platform/metrics-charts.md)分析收集的指标，并在图表上绘制这些指标。 通过将图表固定到 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)来跟踪资源（例如 VM、网站或逻辑应用）的性能。
- 配置指标[警报规则](alerts-metric.md)，以便在指标超过阈值时发送通知或执行[自动化操作](action-groups.md)。
- 根据超过阈值的指标，使用[自动缩放](../../azure-monitor/platform/autoscale-overview.md)来增加或减少资源。
- 将指标连同日志数据一起路由到 Log Analytics 以分析指标数据，并可将指标值存储 93 天以上。
- 将指标流式传输到[事件中心](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)，以便路由到 [Azure 流分析](../../stream-analytics/stream-analytics-introduction.md)或外部系统。
- 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](../../azure-monitor/learn/tutorial-archive-data.md)。
- 使用 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) 或 [REST API](../../azure-monitor/platform/rest-api-walkthrough.md) 从命令行或自定义应用程序访问指标值。

### <a name="viewing-metrics"></a>查看指标
Azure Monitor 中的指标存储在一个经优化的时序数据库中，支持快速检索，并可将指标值存储 93 天。 可以将指标复制到日志以进行长期分析和了解长期趋势。

如前所述，指标数据有多种用法。 使用[指标资源管理器](../../azure-monitor/platform/metrics-charts.md)直接分析指标存储中的数据，并在图表中绘制多个指标在不同时间段的值。 可以通过交互方式查看图表，也可以将其固定到某个仪表板，与其他可视化效果一起查看。 还可以使用 [Azure 监视 REST API](../../azure-monitor/platform/rest-api-walkthrough.md) 检索指标。

![指标资源管理器](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>日志
日志包含不同类型的已经整理成记录的数据，每种类型都有不同的属性集。 日志可能包含数字值（例如指标），但通常包含带详细说明的文本数据。 日志不同于指标之处还在于，日志有结构差异，且通常不按固定时间间隔收集。

常见类型的日志项是偶尔收集的事件。 事件是由应用程序或服务创建的，通常包含足够的信息，其本身提供的上下文已经很完整。 例如，事件可能会指示特定资源已创建或修改、新主机开始响应流量增高的情况，或者在应用程序中检测到了错误。

如果需要将多个源的数据组合起来进行复杂的分析并推断一段时间的趋势，则日志特别有用。 考虑到数据的格式可能有差异，应用程序可以使用所需结构创建自定义日志。 甚至可以在日志中复制指标，以便将其与其他监视数据组合起来，进行趋势推断和其他数据分析。

### <a name="sources-of-log-data"></a>日志数据的源
Azure Monitor 可从 Azure 和本地资源中的各种源收集日志数据。 日志数据的来源包括：

- Windows 和 Linux 虚拟机上的代理，这些代理会根据所配置的数据源，将遥测数据从来宾操作系统和应用程序发送到 Azure Monitor。
- 来自监视解决方案或功能（如 Container Insights、VM Insights 或 Resource Group Insights）的数据，这些数据提供针对特定应用程序或服务的见解。
- [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)收集的安全数据。
- 来自 Azure 资源的[指标](#metrics)。 在 Log Analytics 中可将指标存储 93 天以上，并可以连同其他日志数据一起分析指标。
- 写入到 Azure 存储的遥测数据。

![日志概述](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>日志有哪些作用？
可以使用日志执行的任务包括：

- 使用 Azure 门户中的 Log Analytics 编写用于分析日志数据的查询。 将以表格或图表形式呈现的结果固定到 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)。
- 配置日志警报规则，以便在查询结果与特定的结果匹配时发送通知或执行[自动化操作](action-groups.md)。
- 使用[逻辑应用](~/articles/logic-apps/index.yml)基于日志数据生成工作流。
- 将查询结果导出到 Power BI，以使用不同的可视化效果并与 Azure 外部的用户共享。
- 使用 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) 或 [REST API](https://dev.loganalytics.io/) 从命令行或自定义应用程序访问指标值。


## <a name="convert-monitoring-data"></a>转换监视数据

### <a name="metrics-to-logs"></a>指标到日志
可将指标复制到日志，以使用 Azure Monitor 的丰富查询语言针对其他数据类型执行复杂的分析。 另外，日志数据的保留时间也长于指标，适合进行一段时间的趋势推断。 可以使用指标进行近实时分析和警报发送，可以使用日志和其他数据进行趋势推断和分析。


### <a name="logs-to-metrics"></a>从日志传输到指标
如前所述，指标的响应速度快于日志，因此在创建警报时延迟更低，成本也更低。 很多数字数据本来是适合用作指标的，但并未作为指标存储在 Azure Monitor 中。 一个常见的示例是从代理和管理解决方案收集的性能数据。 某些这样的数据可以复制到指标中，以便通过指标资源管理器发送警报以及进行分析。

有关此功能的说明，请参见[在 Azure Monitor 中为日志创建指标警报](../../azure-monitor/platform/alerts-metric-logs.md)。 值支持列表可以在 [Azure Monitor 支持的指标](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)中获得。

## <a name="stream-data-to-external-systems"></a>将数据流式传输到外部系统
除了使用 Azure 中的工具分析监视数据以外，可能还需要将这些数据转发到外部工具，例如某个安全信息和事件管理 (SIEM) 产品。 通常，这种转发是通过 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)直接从受监视资源完成的。

若要了解不同类型的监视数据，可参阅[将 Azure 监视数据流式传输到事件中心供外部工具使用](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)。


