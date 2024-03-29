---
title: 新建 Azure Application Insights 资源 | Azure Docs
description: 为新的实时应用程序手动设置 Application Insights 监视。
services: application-insights
documentationcenter: ''
author: lingliw
manager: digimobile
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 6/4/2019
ms.author: v-lingwu
ms.openlocfilehash: e73eb2aa581f2cce9525e1ec1a64e69f2fc4ca18
ms.sourcegitcommit: 5fc46672ae90b6598130069f10efeeb634e9a5af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67236557"
---
# <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
Azure Application Insights 在世纪互联 Azure 资源  中显示有关应用程序的数据。 因此创建新资源属于[设置 Application Insights 来监视新应用程序][start]的一个环节。 在许多情况下，IDE 可以自动完成资源创建。 但在某些情况下需要手动创建资源（例如，创建分别用于应用程序开发和产品构建的资源）。

创建资源后，可以获取其检测密钥，并将其用于设置应用程序中的 SDK。 资源密钥将遥测数据链接到资源。

## <a name="sign-up-to-21vianet-azure"></a>注册到世纪互联 Azure
如果没有 [Microsoft 帐户，请立即注册一个](https://live.com)。 （如果使用 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服务，则已获得了一个 Azure 帐户。）

还需要订阅[世纪互联 Azure](https://www.azure.cn)。 如果团队或组织拥有 Azure 订阅，则所有者可以使用 Windows Live ID 你将加入其中。 仅对使用的内容收取费用。 默认基本计划允许一定量的免费试用。

获取订阅的访问权限后，请在 [https://portal.azure.cn](https://portal.azure.cn) 中使用 Live ID 登录到 Application Insights。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
在 [portal.azure.cn](https://portal.azure.cn) 中，添加 Application Insights 资源：

![依次单击“新建”、“Application Insights”](./media/create-new-resource/01-new.png)

* **应用程序类型**会影响在概述边栏选项卡上看到的内容和[指标资源管理器][metrics]中的可用属性。 如果未看到应用类型，请选择“常规”。
* **订阅**是 Azure 中的付款帐户。
* **资源组**便于管理访问控制之类的属性。 如果已创建其他 Azure 资源，可以选择将此新资源放在同一个组中。
* **位置**是保留数据的位置。
* **固定到仪表板**将资源的快速访问磁贴放在 Azure 主页上。 推荐。

创建应用后，将打开一个新边栏选项卡。 可在此边栏选项卡中看到有关应用的性能和使用情况数据。 

要在下次登录 Azure 时返回该应用，请在开始面板（主屏幕）上查找应用的快速启动磁贴。 或单击“浏览”找到它。

## <a name="copy-the-instrumentation-key"></a>复制检测密钥
检测密钥标识所创建的资源。 需要将它提供给 SDK。

![单击“Essentials”、单击“检测密钥，并按“CTRL+C”](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>在应用中安装 SDK
在应用中安装 Application Insights SDK。 此步骤在很大程度上依赖于应用程序的类型。 

使用检测密钥来配置[在应用程序中安装的 SDK][start]。

SDK 包含无需编写任何代码即可发送遥测数据的标准模块。 若要跟踪用户操作或更细致地诊断问题，请[使用 API][api] 发送自己的遥测数据。

<a name="monitor"></a>
## <a name="see-telemetry-data"></a>查看遥测数据
关闭快速启动边栏选项卡，返回到 Azure 门户中的应用程序边栏选项卡。

单击“搜索”磁贴查看[诊断搜索][diagnostic]，其中显示了前几个事件。 

如果需要更多数据，请在几秒后单击“刷新”  。

## <a name="creating-a-resource-automatically"></a>自动创建资源
可以编写 [PowerShell 脚本](../../azure-monitor/app/powershell.md)来自动创建资源。

## <a name="next-steps"></a>后续步骤
* [诊断搜索](../../azure-monitor/app/diagnostic-search.md)
* [探索指标](../../azure-monitor/app/metrics-explorer.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md





