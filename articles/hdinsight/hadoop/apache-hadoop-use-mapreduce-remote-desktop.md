---
title: 将 MapReduce 和远程桌面与 HDInsight 中的 Apache Hadoop 配合使用 - Azure
description: 了解如何使用远程桌面连接到 HDInsight 上的 Apache Hadoop 并运行 MapReduce 作业。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 01/12/2017
ms.date: 02/04/2019
ms.author: v-yiso
ROBOTS: NOINDEX
ms.openlocfilehash: 1ed39ae0a892d0d63c8a2c244b21fc63a5cad19e
ms.sourcegitcommit: 0cb57e97931b392d917b21753598e1bd97506038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906089"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight-with-remote-desktop"></a>通过远程桌面在 HDInsight 上的 Apache Hadoop 中使用 MapReduce
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

本文介绍如何通过使用远程桌面连接到 Apache Hadoop on HDInsight 群集，然后使用 Hadoop 命令运行 MapReduce 作业。

> [!IMPORTANT]
> 远程桌面只能在基于 Windows 的 HDInsight 群集上使用。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。
>
> 有关 HDInsight 3.4 或更高版本，请参阅[将 MapReduce 与 SSH 配合使用](apache-hadoop-use-mapreduce-ssh.md)，了解如何连接到 HDInsight 群集以及如何运行 MapReduce 作业。

## <a id="prereq"></a>先决条件
要完成本文中的步骤，需要：

* 基于 Windows 的 HDInsight（HDInsight 上的 Hadoop）群集
* 运行 Windows 10、Windows 8 或 Windows 7 的客户端计算机

## <a id="connect"></a>使用远程桌面进行连接
为 HDInsight 群集启用远程桌面，然后根据[使用 RDP 连接到 HDInsight 群集](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)中的说明连接到该群集。

## <a id="hadoop"></a>使用 Hadoop 命令
连接到 HDInsight 群集的桌面之后，请使用以下步骤，以通过 Hadoop 命令来运行 MapReduce 作业：

1. 从 HDInsight 桌面启动“Hadoop 命令行”。 这将在 c:\apps\dist\hadoop-&lt;version number> 目录中打开新的命令提示符。

   > [!NOTE]
   > 版本号会随着 Hadoop 更新而更改。 HADOOP_HOME 环境变量可用于查找路径。 例如，`cd %HADOOP_HOME%` 会将目录更改为 Hadoop 目录，而不需要你知道版本号。
   >
   >
2. 若要使用 **Hadoop** 命令运行示例 MapReduce 作业，请使用以下命令：

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    这将启动 wordcount 类（包含在当前目录中的 hadoop-mapreduce-examples.jar 文件内）。 作为输入，它使用 wasbs://example/data/gutenberg/davinci.txt 文档，输出的存储位置：wasbs:///example/data/WordCountOutput。

   > [!NOTE]
   > 有关此 MapReduce 作业和示例数据的详细信息，请参阅<a href="hdinsight-use-mapreduce.md">在 HDInsight Hadoop 中使用 MapReduce</a>。
   >
   >
3. 作业在处理时提供详细信息，并在完成时返回如下信息：

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. 作业完成之后，使用以下命令行列出存储在 **wasbs://example/data/WordCountOutput** 的输出文件：

        hadoop fs -ls wasb:///example/data/WordCountOutput

    这应会显示两个文件：**_SUCCESS** 和 **part-r-00000**。 **part-r-00000** 文件包含此作业的输出。

   > [!NOTE]
   > 某些 MapReduce 作业可能会将结果拆分成多个 **part-r-#####** 文件。 如果是这样，请使用 ##### 后缀指示文件的顺序。
   >
   >
5. 若要查看输出，请使用以下命令：

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    这会显示 **wasbs://example/data/gutenberg/davinci.txt** 文件中包含的单词列表，以及每个单词出现的次数。 下面是要包含在文件中的数据示例：

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>摘要
Hadoop 命令提供了一种简单方法，可在 HDInsight 群集上运行 MapReduce 作业，并查看作业输出。

## <a id="nextsteps"></a>后续步骤
有关 HDInsight 中的 MapReduce 作业的一般信息：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

有关 HDInsight 上 Hadoop 的其他使用方法的信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
