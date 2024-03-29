---
title: 使用 Ambari API 在 HDInsight 中监视 Hadoop 群集 - Azure
description: 使用 Apache Ambari API 创建、管理和监视 Hadoop 群集。 直观的操作员工具和 API 消除了 Hadoop 的复杂性。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
origin.date: 04/07/2017
ms.date: 04/15/2019
ms.author: v-yiso
ROBOTS: NOINDEX
ms.openlocfilehash: 632f9c55242c9ee6be837e92f28502858ebc5c82
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004109"
---
# <a name="monitor-apache-hadoop-clusters-in-hdinsight-using-the-apache-ambari-api"></a>使用 Apache Ambari API 在 HDInsight 中监视 Apache Hadoop 群集
了解如何使用 Apache Ambari API 监视 HDInsight 群集。

> [!NOTE]
> 本文中的信息主要针对提供 Ambari REST API 只读版本的基于 Windows 的 HDInsight 群集。 对于基于 Linux 的群集，请参阅[使用 Apache Ambari 管理 Apache Hadoop 群集](hdinsight-hadoop-manage-ambari.md)。
> 
> 

## <a name="what-is-ambari"></a>什么是 Ambari？
[Apache Ambari][ambari-home] 用于预配、管理和监视 Apache Hadoop 群集。 它包括一系列直观的操作员工具和一组免除 Hadoop 复杂性的可靠 API，可简化群集操作。 有关这些 API 的详细信息，请参阅 [Ambari API 参考][ambari-api-reference]。 

HDInsight 目前仅支持 Ambari 监视功能。 Ambari API 1.0 受 HDInsight 版本 3.0 和 2.1 群集支持。 本文介绍如何访问 HDInsight 版本 3.1 和 2.1 群集上的 Ambari API。 两者之间的主要区别在于，随着新功能（例如作业历史记录服务器）的引入，某些组件已发生更改。 

**先决条件**

要阅读本教程，必须具备以下项：

* **配备 Azure PowerShell 的工作站**。
* （可选）[cURL][curl]。 若要安装它，请参阅 [cURL 版本和下载][curl-download]。
  
  > [!NOTE]
  > 在 Windows 中使用 cURL 命令时，对选项值使用双引号而非单引号。
  > 
  > 
* **一个 Azure HDInsight 群集**。 有关群集预配的说明，请参阅[开始使用 HDInsight][hdinsight-get-started] 或[预配 HDInsight 群集][hdinsight-provision]。 需要以下数据才能完成本教程：
  
  | 群集属性 | Azure PowerShell 变量名 | 值 | 说明 |
  | --- | --- | --- | --- |
  |   HDInsight 群集名称 |$clusterName | |你的 HDInsight 群集的名称。 |
  |   群集用户名 |$clusterUsername | |创建群集时指定的群集用户名。 |
  |   群集密码 |$clusterPassword | |群集用户密码。 |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>快速开始
使用 Ambari 来监视 HDInsight 群集有几种方法。

**使用 Azure PowerShell**

以下 Azure PowerShell 脚本可在 HDInsight 3.5 群集中获取 MapReduce 作业跟踪器信息。  主要区别在于，我们从 YARN 服务（而非 MapReduce）中拉取这些详细信息。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

以下 PowerShell 脚本可 *在 HDInsight 2.1 群集*中获取 MapReduce 作业跟踪器信息：

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

输出为：

![作业跟踪器输出][img-jobtracker-output]

**使用 cURL**

以下示例可使用 cURL 获取群集信息：

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

输出为：

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**对于 2014/10/8 版本**：

使用 Ambari 终结点“https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}”时，*host_name* 字段会返回节点的完全限定域名 (FQDN)，而不是主机名。 在 2014/10/8 版本之前，此示例仅返回 "**headnode0**"。 在 2014/10/8 版本之后，将获取 FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**"，如以上示例所示。 需要这种改变促进实现可以在一个虚拟网络 (VNET) 中部署多个群集类型（如 HBase 和 Hadoop）的方案。 例如，使用 HBase 作为 Hadoop 的后端平台时，会发生这种情况。

## <a name="ambari-monitoring-apis"></a>监视 API 的 Ambari
下表列出了一些最常用的 Ambari 监视 API 调用。 有关该 API 的详细信息，请参阅 [Apache Ambari API 参考][ambari-api-reference]。

| 监视 API 调用 | URI | 说明 |
| --- | --- | --- |
| 获取群集 |`/api/v1/clusters` | |
| 获取群集信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |群集、服务、主机 |
| 获取服务 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |服务包括：hdfs、mapreduce |
| 获取服务信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| 获取服务组件 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS：namenode、datanodeMapReduce：jobtracker；tasktracker |
| 获取组件信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo、主机组件、指标 |
| 获取主机 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0、workernode0 |
| 获取主机信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| 获取主机组件 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode、resourcemanager |
| 获取主机组件信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles、组件、主机、指标 |
| 获取配置 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |配置类型：core-site、hdfs-site、mapred-site、hive-site |
| 获取配置信息 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |配置类型：core-site、hdfs-site、mapred-site、hive-site |

## <a name="next-steps"></a>后续步骤
现在已经学习了如何使用 Apache Ambari 监视 API 调用。 若要了解更多信息，请参阅以下文章：

* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight 群集][hdinsight-admin-powershell]
* [使用命令行界面管理 HDInsight 群集][hdinsight-admin-cli]
* [HDInsight 文档][hdinsight-documentation]
* [开始使用 HDInsight][hdinsight-get-started]

[ambari-home]: https://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: https://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
