---
title: 通过堆转储调试和分析 Hadoop 服务 - Azure | Azure
description: 自动收集 Hadoop 服务的堆转储并将其放置在 Azure Blob 存储帐户内用于调试和分析。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/25/2017
ms.date: 01/21/2019
ms.author: v-yiso
ROBOTS: NOINDEX
ms.openlocfilehash: b65adf9cec87f48e44fd8dd9f3a4174080889da8
ms.sourcegitcommit: f159d58440b39f5f591dae4e92e6f4d500ed3fc1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54216232"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>在 Blob 存储中收集堆转储以调试和分析 Hadoop 服务
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆转储包含应用程序的内存快照，其中包括创建转储时各变量的值。 因此，它们在诊断发生在运行时的问题时很有用。 可以自动收集 [Apache Hadoop](https://hadoop.apache.org/) 服务的堆转储，并将其放置在用户的 Azure Blob 存储帐户中的 HDInsightHeapDumps/ 下。

必须为各个群集上的服务启用各种服务的堆转储集合。 默认为群集关闭此功能。 这些堆转储可能很大，因此在启用收集后，建议监视保存这些转储的 Blob 存储帐户。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。 本文中的信息仅适用于基于 Windows 的 HDInsight。 有关基于 Linux 的 HDInsight 的信息，请参阅[为基于 Linux 的 HDInsight 上的 Apache Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)。

## <a name="eligible-services-for-heap-dumps"></a>符合启用堆转储的服务
可以启用以下服务的堆转储：

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - resourcemanager、nodemanager、timelineserver
* **Apache hdfs** - datanode、secondarynamenode、namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>用于启用堆转储的配置元素
若要为服务启用堆转储，需要在该服务的节（由 **service_name** 指定）中设置相应的配置元素。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service_name** 的值可以是此处列出的任何服务：tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode 或 namenode。

## <a name="enable-using-azure-powershell"></a>使用 Azure PowerShell 启用
例如，要使用 Azure PowerShell 为 jobhistoryserver 启用堆转储，可以使用以下脚本：

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>使用 .NET SDK 启用
例如，要使用 Azure HDInsight .NET SDK 为 jobhistoryserver 启用堆转储，可以使用以下代码：

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
