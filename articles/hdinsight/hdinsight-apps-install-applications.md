---
title: 在 Azure HDInsight 上安装第三方应用程序
description: 了解如何在 Azure HDInsight 上安装第三方 Hadoop 应用程序。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/01/2019
ms.date: 04/01/2019
ms.author: v-yiso
ms.openlocfilehash: 0551821b41ceb59cd11f13a36b3d1d317b2f8b0c
ms.sourcegitcommit: 99ef971eb118e3c86a6c5299c7b4020e215409b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65829364"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序

了解如何在 Azure HDInsight 上安装第三方 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

HDInsight 应用程序是用户可以在 HDInsight 群集上安装的应用程序。 这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

以下列表显示已发布的应用程序：

|应用程序 |群集类型 | 说明 |
|---|---|---|
|AtScale 智能平台 |Hadoop |AtScale 可将 HDInsight 群集转换成 OLAP 横向扩展服务器，使你能够使用熟悉的、自有的或偏好的 BI 工具（包括 Microsoft Excel、PowerBI、Tableau Software、QlikView 等等）以交互方式查询数十亿行数据。 |
|适用于 HDInsight 的 CDAP |HBase |CDAP 是第一款用于处理大数据的统一集成平台，可以加速的 Hadoop 的价值实现，使 IT 人员能够提供自助服务数据。 CDAP 采用开源设计并且可扩展，消除了创新所面临的障碍。 要求：4 个区域节点，至少需要安装 D3 v2。 |
|Datameer |Hadoop |Datameer 是一个可缩放的自助服务平台，用于准备、浏览和管理要分析的数据，可以加速将复杂多源数据转变为随时可在业务中使用的宝贵信息，以企业规模提供更快、更智能的见解。 |
|HDInsight 上的 Dataiku DSS |Hadoop、Spark |企业数据科学平台中的 Dataiku DSS，可让数据科学家和数据分析师更有效地协作设计和运行新的数据产品与服务，将原始数据转变为有影响力的预测结果。 |
|WANdisco Fusion HDI 应用 |Hadoop、Spark、HBase、Storm、Kafka |在分布式环境中保持数据一致性是一个很大的数据操作难题。 企业级的软件平台 WANdisco Fusion 可在任一环境中实现非结构化数据的一致性，从而可以解决此问题。 |
|适用于 HDInsight 的 H2O SparklingWater |Spark |H2O Sparkling Water 支持以下分布式算法：GLM、朴素贝叶斯、分布式随机森林、梯度提升机、深度神经网络、深度学习、K 平均、PCA、广义低秩模型、异常情况检测和自编码器。 |
|用于将实时数据与 HDInsight 集成的 Striim |Hadoop、HBase、Storm、Spark、Kafka |Striim（读作“stream”）是一个端到端流式处理数据集成和智能平台，可用于实现不同数据流的持续引入、处理和分析。 |
|Jumbune Enterprise - 加速大数据分析 |Hadoop、Spark |在较高层面，Jumbune 可通过以下方式为企业提供辅助：1. 加速基于 Tez、MapReduce 和 Spark 引擎的 Hive、Java 和 Scala 工作负荷性能。 2. 主动监视 Hadoop 群集。3. 在分布式文件系统中建立数据质量管理。 |
|Kyligence Enterprise |Hadoop、HBase、Spark |Kyligence Enterprise 以 Apache Kylin 为后盾，可以基于大数据实现商业智能 (BI)。 作为 Hadoop 上的企业级 OLAP 引擎，Kyligence Enterprise 可让业务分析师使用行业标准的数据仓库和 BI 方法在 Hadoop 上构建 BI。 |
|适用于 KNIME Spark 执行器的 Spark 作业服务器 |Spark |适用于 KNIME Spark 执行器的 Spark 作业服务器用于将 KNIME Analytics Platform 连接到 HDInsight 群集。 |
|适用于 Azure HDInsight 的 Starburst Presto、Starburst Presto |Hadoop |Presto 是一个快速的可缩放分布式 SQL 查询引擎。 Presto 针对存储和计算的分离进行了架构设计，非常适用于查询 Azure Data Lake Storage、Azure Blob 存储、SQL 和 NoSQL 数据库以及其他数据源中的数据。 |
|适用于 HDInsight 云的 StreamSets 数据收集器 |Hadoop、HBase、Spark、Kafka |StreamSets 数据收集器是一个轻量的强大引擎，可以实时流式传输数据。 使用数据收集器可以路由和处理数据流中的数据。 该产品附带 30 天试用许可证。 |
|[Trifacta Wrangler Enterprise](https://www.trifacta.com/) |Hadoop、Spark、HBase |Trifacta Wrangler Enterprise for HDInsight 支持对任何规模的数据进行企业级的数据整理。 在 Azure 上运行 Trifacta 的成本包括 Trifacta 订阅成本加上虚拟机的 Azure 基础结构成本。 |
|Unifi Data Platform |Hadoop、HBase、Storm、Spark |Unifi Data Platform 是一个无缝集成的自助服务数据工具套件，旨在帮助企业用户解决推动收入增长、降低成本或运营复杂性的数据难题。 |
|Unraveldata APM |Spark |适用于 HDInsight Spark 群集的 Unravel Data 应用。 |
|Waterline AI 驱动的数据目录 |Spark |Waterline 使用 AI 来编录、组织和管理数据以使用业务术语自动标记数据。 Waterline 的商业文献目录是一个用于自助分析、合规性和管理，以及 IT 管理计划的关键成功组件。 |

本文提供的说明将使用 Azure 门户。 也可以从门户导出 Azure 资源管理器模板或从供应商处获取 Resource Manage 模板的副本，并使用 Azure PowerShell 和 Azure 经典 CLI 部署模板。  请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>先决条件
如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-applications-to-existing-clusters"></a>将应用程序安装到现有群集
下面的过程演示如何将 HDInsight 应用程序安装到现有的 HDInsight 群集。

**安装 HDInsight 应用程序**

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”。
3. 在列表中选择一个 HDInsight 群集。  如果没有群集，必须先创建一个。  请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
4. 在“设置”类别下，选择“应用程序”。 主窗口中会显示已安装的应用程序列表。 

    ![HDInsight 应用程序门户菜单](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 在菜单中选择“+添加”。 此时会显示可用应用程序的列表。  如果“+添加”灰显，则表示没有任何应用程序适用于此版本的 HDInsight 群集。
   
    ![HDInsight 应用程序可用应用程序](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. 选择某个可用的应用程序，然后遵照说明接受法律条款。

可以通过门户通知查看安装状态（选择门户顶部的铃铛图标）。 安装应用程序后，应用程序会出现在“已安装的应用”列表中。

## <a name="install-applications-during-cluster-creation"></a>在群集创建期间安装应用程序
可以选择在创建群集时安装 HDInsight 应用程序。 在此过程中，HDInsight 应用程序会在群集创建并处于运行状态后安装。 若要在群集创建过程中使用 Azure 门户安装应用程序，请使用“自定义”选项而不是默认的“快速创建”选项。

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安装的 HDInsight 应用和属性
门户会显示群集的已安装 HDInsight 应用程序列表，以及每个已安装应用程序的属性。

**列出 HDInsight 应用程序并显示属性**

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”。
3. 在列表中选择一个 HDInsight 群集。
4. 在“设置”类别下，选择“应用程序”。 主窗口中会显示已安装的应用程序列表。 
   
    ![HDInsight 应用程序已安装的应用](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
    
5. 选择某个已安装的应用程序以显示属性。 属性列表：

    |属性 | 说明 |
    |---|---|
    |应用程序名称 |应用程序名称。 |
    |状态 |应用程序状态。 |
    |网页 |已部署到边缘节点的 Web 应用程序的 URL。 此凭据与你针对群集配置的 HTTP 用户凭据相同。 |
    |SSH 终结点 |可以使用 SSH 连接到边缘节点。 SSH 凭据与你针对群集配置的 SSH 用户凭据相同。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |说明 | 应用程序说明。 |

6. 若要删除应用程序，请右键单击应用程序，并单击上下文菜单中的“删除”。

## <a name="connect-to-the-edge-node"></a>连接到边缘节点
可以使用 HTTP 和 SSH 连接到边缘节点。 可在 [门户](#list-installed-hdinsight-apps-and-properties)中找到终结点信息。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 终结点凭据是针对 HDInsight 群集配置的 HTTP 用户凭据；SSH 终结点凭据是针对 HDInsight 群集配置的 SSH 凭据。

## <a name="troubleshoot"></a>故障排除
请参阅 [故障排除安装问题](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>后续步骤
* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md)（安装自定义 HDInsight 应用程序）：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。
<!--Update_Description: wording update-->