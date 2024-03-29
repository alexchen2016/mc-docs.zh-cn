---
title: 在 Java Web 项目中排查 Application Insights 问题
description: 故障排除指南 - 使用 Application Insights 监视实时 Java 应用。
services: application-insights
documentationcenter: java
author: lingliw
manager: digimobile
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 6/4/2019
ms.author: v-lingwu
ms.openlocfilehash: 03d63e50f3a79ea38547162bd77ca8287fb31149
ms.sourcegitcommit: 4c10e625a71a955a0de69e9b2d10a61cac6fcb06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67046897"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>用于 Java 的 Application Insights 的故障排除与常见问题解答
使用 [Java 中的 Azure Application Insights][java] 时有疑问或遇到问题？ 请参考下面的提示。

## <a name="build-errors"></a>生成错误
**在 Eclipse 或 Intellij Idea 中，通过 Maven 或 Gradle 添加 Application Insights SDK 时，收到生成或校验和验证错误。**

* 如果依赖项 `<version>` 元素使用包含通配符字符的模式（例如(Maven) `<version>[2.0,)</version>` 或 (Gradle) `version:'2.0.+'`），则尝试改为指定特定版本，如 `2.0.1`。 请参阅最新版本的[发行说明](https://github.com/Microsoft/ApplicationInsights-Java/releases)。

## <a name="no-data"></a>没有数据
**我已成功添加 Application Insights 并运行应用，但在门户中从未看到数据。**

* 请稍等片刻，并单击“刷新”。 图表会定期自行刷新，但你也可以手动刷新。 刷新间隔取决于图表的时间范围。
* 检查是否已在 ApplicationInsights.xml 文件（位于项目的 resources 文件夹）中定义检测密钥或将检测密钥配置为环境变量。
* 确认 xml 文件中没有 `<DisableTelemetry>true</DisableTelemetry>` 节点。
* 在防火墙中，可能需要打开 TCP 端口 80 和 443 才能将传出流量发送到 dc.services.visualstudio.com。 请参阅 [full list of firewall exceptions](../../azure-monitor/app/ip-addresses.md)（防火墙例外的完整列表）
* 在世纪互联 Azure 开始面板中查看服务状态映射。 如果看到警报指示，请等待它们恢复“正常”，关闭再重新打开 Application Insights 应用程序边栏选项卡。
* 在 ApplicationInsights.xml 文件（位于项目的 resources 文件夹）中的根节点下添加 `<SDKLogger />` 元素，打开 IDE 控制台窗口日志记录功能，并检查条目的前面是否带有 AI：INFO/WARN/ERROR 以发现任何可疑日志。
* 查看控制台输出消息中是否包含“已成功找到配置文件”语句，确保 Java SDK 成功加载正确的 ApplicationInsights.xml 文件。
* 如果找不到配置文件，请检查输出消息来确定在何处搜索配置文件，并确保 ApplicationInsights.xml 位在这些搜索位置之一。 根据经验法则，可以将配置文件放置在 Application Insights SDK JAR 的附近。 例如：在 Tomcat 中，这可能是 WEB-INF/classes 文件夹。 在开发期间，可以将 ApplicationInsights.xml 放在 Web 项目的 resources 文件夹中。
* 另请查看 [GitHub 问题页](https://github.com/Microsoft/ApplicationInsights-Java/issues)，了解 SDK 的已知问题。
* 请确保使用相同版本的 Application Insights Core、Web、代理和日志记录追加器以避免任何版本冲突问题。

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>我以前看到了数据，但现在看不到
* 请查看[状态博客](https://blogs.msdn.com/b/applicationinsights-status/)。
* 是否达到了数据点的每月配额？ 打开“设置/配额和定价”即可检查。如果达到了配额，可以升级计划，或付费购买更多的容量。 请参阅[定价方案](https://www.azure.cn/pricing/details/application-insights/)。
* 最近是否升级了 SDK？ 请确保项目目录内仅存在唯一 SDK jar。 不应存在两个不同版本的 SDK。
* 是否正在查看正确的 AI 资源？ 请将应用程序的 iKey 与预期遥测的资源的 iKey 相匹配。 它们应相同。

#### <a name="i-dont-see-all-the-data-im-expecting"></a>未按预期看到所有数据
* 打开“使用情况和预估成本”页面并检查[采样](../../azure-monitor/app/sampling.md)是否正在进行。 （如果传输百分比为 100%，表示当前未执行采样。）可将 Application Insights 服务设置为只接受来自应用的一部分遥测数据。 这有助于保持在每月的遥测配额范围内。
* 是否已启用 SDK 采样？ 如果是，将按为所有适用类型指定的速率对数据进行采样。
* 是否正在运行较旧版本的 Java SDK？ 从版本 2.0.1 开始，我们引入了容错机制以处理间歇性网络和后端故障，以及本地驱动器上的数据持久性。
* 是否由于过度遥测而受到限制？ 如果启用“信息日志记录”，会看到日志消息“应用受到限制”。 当前的限制为 32000 个遥测项/秒。

### <a name="java-agent-cannot-capture-dependency-data"></a>Java 代理无法捕获依赖项数据
* 是否已按照[配置 Java 代理](java-agent.md)配置 Java 代理？
* 请确保 java 代理 jar 和 AI-Agent.xml 文件放置在同一文件夹中。
* 请确保自动收集功能支持你尝试自动收集的依赖项。 目前我们仅支持 MySQL、MsSQL、Oracle DB 和 用于 Redis 的 Azure 缓存依赖项收集。
* 使用的是 JDK 1.7 还是 JDK 1.8？ 目前我们在 JDK 9 中不支持依赖项收集。

## <a name="no-usage-data"></a>无使用情况数据
**我看到了请求和响应时间的相关数据，但没有看到页面视图、浏览器或用户数据。**

已成功将应用设置为从服务器发送遥测数据。 下一步是[将网页设置为从 Web 浏览器发送遥测数据][usage]。

或者，如果客户端是[手机或其他设备][platforms]中的应用，可以从该处发送遥测数据。

使用相同的检测密钥来设置客户端和服务器遥测。 数据将出现在相同的 Application Insights 资源中，可以将来自客户端和服务器的事件相关联。


## <a name="disabling-telemetry"></a>禁用遥测
**如何禁用遥测数据收集？**

在代码中：

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**或**

更新 ApplicationInsights.xml（位于项目的 resources 文件夹中）。 在根节点下添加以下代码：

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

如果使用 XML 方法，则必须在更改值后重新启动应用程序。

## <a name="changing-the-target"></a>更改目标
**如何更改项目要将数据发送到的 Azure 资源？**

* [获取新资源的检测密钥][java]
* 如果使用用于 Eclipse 的 Azure 工具包将 Application Insights 添加到项目，请右键单击 Web 项目，选择“Azure”、“配置 Application Insights”，然后更改密钥。  
* 如果已将检测密钥配置为环境变量，请使用新 iKey 更新环境变量的值。
* 否则，请更新项目的 resources 文件夹中 ApplicationInsights.xml 内的密钥。

## <a name="debug-data-from-the-sdk"></a>通过 SDK 调试数据

**如何知道 SDK 正在执行什么操作？**

若要获取有关 API 中的具体操作的详细信息，请在 ApplicationInsights.xml 配置文件的根节点下添加 `<SDKLogger/>`。

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

也可以指示记录器将信息输出到某个文件：

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

若要使用 Application Insight Spring Boot Starter 启用 Spring Boot 应用的 SDK 日志记录，请将以下内容添加到 `application.properties` 文件中：

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.azure.cn/v2/track
```

### <a name="java-agent"></a>Java 代理

若要启用 JVM 代理日志记录，请更新 [AI-Agent.xml 文件](java-agent.md)。

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>Azure 开始屏幕
**我正在查看 [Azure 门户](https://portal.azure.cn)。地图是否告知有关应用的信息？**

不会，它只显示世界各地的 Azure 服务器的运行状况。

*如何从 Azure 开始面板（主屏幕）找到有关应用的数据？*

假设要[为 Application Insights 设置应用][java]，请单击“浏览”，选择“Application Insights”，并选择为应用创建的应用资源。 今后要快速转到该位置，可将应用固定到开始面板。

## <a name="intranet-servers"></a>Intranet 服务器
**是否可以在 Intranet 上监视服务器？**

可以，前提是该服务器可以通过公共 Internet 将遥测数据发送到 Application Insights 门户。

在防火墙中，可能需要打开 TCP 端口 80 和 443 才能将传出流量发送到 dc.services.visualstudio.com 和 f5.services.visualstudio.com。

## <a name="data-retention"></a>数据保留
**数据在门户中保留多长时间？是否安全？**

请参阅[数据保留和隐私][data]。

## <a name="debug-logging"></a>调试日志记录
Application Insights 使用 `org.apache.http`。 这将在命名空间 `com.microsoft.applicationinsights.core.dependencies.http` 下的 Application Insights 核心 jar 中重定位。 这将允许 Application Insights 处理一段基本代码中存在不同版本的同一 `org.apache.http` 的方案。

>[!NOTE]
>如果为应用中的所有命名空间启用了调试级别日志记录，则所有执行中模块（包括重命名为 `com.microsoft.applicationinsights.core.dependencies.http` 的 `org.apache.http`）都将遵循它。 Application Insights 将无法为这些调用应用筛选，因为进行日志调用的是 Apache 库。 调试级别日志记录将生成大量日志数据，因此不建议在实时生产实例中使用。


## <a name="next-steps"></a>后续步骤
**我为 Java 服务器应用设置了 Application Insights。接下来还可以做些什么？**

* [监视网页的可用性][availability]
* [监视网页的使用情况][usage]
* [跟踪使用情况和诊断设备应用中的问题][platforms]
* [编写代码来跟踪应用的使用情况][track]
* [捕获诊断日志][javalogs]

## <a name="get-help"></a>获取帮助
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md





