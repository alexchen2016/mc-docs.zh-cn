---
title: 以编程方式访问 Hadoop YARN 应用程序日志 - Azure | Azure
description: 以编程方式访问 HDInsight 中 Hadoop 群集上的应用程序日志。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 05/25/2017
ms.date: 12/24/2018
ms.author: v-yiso
ROBOTS: NOINDEX
ms.openlocfilehash: 39a299409338e4baa5b3720717a33394e1bf1607
ms.sourcegitcommit: f159d58440b39f5f591dae4e92e6f4d500ed3fc1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54216237"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-windows-based-hdinsight"></a>在基于 Windows 的 HDInsight 上访问 Apache Hadoop YARN 应用程序日志
本文档介绍了如何访问 Azure HDInsight 中基于 Windows 的 Apache Hadoop 群集上已完成的 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) 应用程序的日志

> [!IMPORTANT]  
> 本文档中的信息仅适用于基于 Windows 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。 有关在基于 Linux 的 HDInsight 群集上访问 YARN 日志的信息，请参阅[在基于 Linux 的 Apache Hadoop on HDInsight 中访问 Apache Hadoop YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs-linux.md)。


### <a name="prerequisites"></a>先决条件
* 基于 Windows 的 HDInsight 群集。  请参阅[在 HDInsight 中创建基于 Windows 的 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="yarn-timeline-server"></a>YARN Timeline Server
<a href="https://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Apache Hadoop YARN Timeline Server</a> 通过两个不同的接口提供已完成应用程序的相关泛型信息，以及框架特定应用程序信息。 具体而言：

* 已通过 3.1.1.374 版或更高版本启用存储和检索 HDInsight 群集的通用应用程序信息。
* Timeline Server 的架构特定应用程序信息组件目前不适用于 HDInsight 群集。

通用应用程序信息包含以下类型的数据：

* 应用程序 ID（应用程序的唯一标识符）
* 启动应用程序的用户
* 尝试完成应用程序的相关信息
* 任何给定应用程序尝试所用的容器

在 HDInsight 群集中，此信息是由 Azure 资源管理器存储的。 信息将保存到群集的默认存储中的历史记录存储区。 可以通过 REST API 检索有关完成应用程序的此类通用数据：

    GET on https://<cluster-dns-name>.azurehdinsight.cn/ws/v1/applicationhistory/apps

## <a name="yarn-applications-and-logs"></a>YARN 应用程序和日志
YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型。 YARN 使用全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM)。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 为其分配容器的进度。 根据应用程序性质，应用程序可能需要多个容器。

* 每个应用程序可能包含多个 *应用程序尝试*。 
* 容器授予给应用程序的特定尝试。 
* 容器为基本工作单元提供了上下文。 
* 在容器的上下文中执行的工作是在容器分配到的单个工作节点上执行的。 

有关详细信息，请参阅 [Apache Hadoop YARN 概念][YARN-concepts]。

应用程序日志（和关联的容器日志）在对有问题的 Hadoop 应用程序进行调试上相当重要。 YARN 提供一个良好的框架，通过使用[日志聚合][log-aggregation]功能收集、聚合和存储应用程序日志。 日志聚合功能让访问应用程序日志更具确定性，因为该功能可聚合辅助节点上所有容器的日志，并在应用程序完成后，将它们按每个辅助节点一个聚合日志的方式存储在默认文件系统上。 应用程序可能使用数百或数千个容器，但在单个工作节点上运行的所有容器的日志将聚合成单个文件，也就是为应用程序所用的每个工作节点生成一个文件。 默认情况下，日志聚合已在 HDInsight 群集（3.0 和更高版本）上启用，在群集的默认容器中，可以找到聚合的日志，位置如下：

    wasb:///app-logs/<user>/logs/<applicationId>

在该位置中，*user* 是启动应用程序的用户名，*applicationId* 是 YARN RM 分配的应用程序唯一标识符。

无法直接阅读聚合日志，因为它们是以 [TFile][T-file]（由容器编制索引的[二进制格式][binary-format]）编写。 YARN 提供 CLI 工具，针对感兴趣的应用程序或容器，将这些日志转储成纯文本。 可以直接在群集节点上（通过 RDP 连接到节点之后）运行以下 YARN 命令之一，以纯文本格式查看这些日志：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI 在群集头节点上运行，可以通过 Azure 门户仪表板进行访问：

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 在左侧菜单中，依次单击“浏览”、“HDInsight 群集”和要在其上访问 YARN 应用程序日志的基于 Windows 的群集。
3. 在顶部菜单中，单击“仪表板” 。 将会看到在新的浏览器选项卡上打开名为“HDInsight 查询控制台”页面。
4. 在“HDInsight 查询控制台”中单击“Yarn UI”。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
<!--Update_Description: change 'wasbs' into 'wasb'-->