---
title: 使用用于 Visual Studio 的 Data Lake (Hadoop) 工具运行 Hive 查询 - Azure HDInsight | Azure
description: 了解如何使用用于 Visual Studio 的 Data Lake 工具对 Azure HDInsight 上的 Apache Hadoop 运行 Apache Hive 查询。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/16/2018
ms.date: 06/10/2019
ms.author: v-yiso
ms.openlocfilehash: b49df67ea02a1df7ee20e2a7b196ff6c4969e822
ms.sourcegitcommit: 58df3823ad4977539aa7fd578b66e0f03ff6aaee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66424683"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询

了解如何使用用于 Visual Studio 的 Data Lake 工具查询 Apache Hive。 使用 Data Lake 工具，可以轻松创建 Hive 查询，将其提交到 Azure HDInsight 上的 Apache Hadoop 并进行监视。

## <a id="prereq"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* Visual Studio（以下版本之一）：

    * Visual Studio 2015、2017（任何版本）
    * Visual Studio 2013 Community/Professional/Premium/Ultimate Update 4

* 用于 Visual Studio 的 HDInsight 工具或用于 Visual Studio 的 Azure Data Lake 工具。 请参阅 [Get started using Visual Studio Hadoop tools for HDInsight](apache-hadoop-visual-studio-tools-get-started.md)（开始使用 Visual Studio Hadoop tools for HDInsight），了解如何安装和配置这些工具。

## <a id="run"></a> 使用 Visual Studio 运行 Apache Hive 查询

可以使用两个选项来创建并运行 Hive 查询：

* 创建即席查询
* 创建 Hive 应用程序

### <a name="ad-hoc"></a>即席

即席查询可以**批处理**或**交互式**模式执行。

1. 打开 **Visual Studio**。

2. 在“服务器资源管理器”中，导航到“Azure” > “HDInsight”。   

3. 展开“HDInsight”，右键单击要运行查询的群集，然后选择“编写 Hive 查询”   。

4. 输入以下 Hive 查询：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 选择“执行”  。 请注意，执行模式默认设置为“交互式”  。

    ![执行交互式 Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 若要以**批处理**模式下运行同一查询，请将下拉列表从“交互式”  切换到“批处理”  。 请注意，执行按钮将从“执行”  更改为“提交”  。

    ![提交 Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入 `SELECT * FROM`，则 IntelliSense 会列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。 IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。

    ![HDInsight Visual Studio Tools IntelliSense 示例 1 的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools IntelliSense 示例 2 的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. 选择“提交”或“提交(高级)”。  

   如果选择高级提交选项，请为脚本配置“作业名称”、“参数”、“其他配置”和“状态目录”：    

    ![HDInsight Hadoop Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "提交查询")

### <a name="hive-application"></a>Hive 应用程序

1. 打开 **Visual Studio**。

2. 在菜单栏中，导航到“文件” > “新建” > “项目”。   

3. 在“新建项目”  窗口中，导航至“模板”   > “Azure Data Lake”   > “HIVE (HDInsight)”   > “Hive 应用程序”  。 

4. 提供此项目的名称，然后选择“确定”  。

5. 打开在创建此项目时产生的 **Script.hql** 文件，并在其中粘贴以下 HiveQL 语句：

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    这些语句执行以下操作：

   * `DROP TABLE`：如果表存在，此语句会将其删除。

   * `CREATE EXTERNAL TABLE`：在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义；数据会保留在原始位置。

     > [!NOTE]
     > 如果希望通过外部源更新基础数据，应使用外部表。 例如，MapReduce 作业或 Azure 服务。
     >
     > 删除外部表 **不会** 删除数据，只会删除表定义。

   * `ROW FORMAT`：让 Hive 知道数据的格式已如何进行了设置。 在此情况下，每个日志中的字段以空格分隔。

   * `STORED AS TEXTFILE LOCATION`：告知 Hive 数据已以文本形式存储在 example/data 目录中。

   * `SELECT`：选择 `t4` 列包含值 `[ERROR]` 的所有行计数。 此语句会返回值 `3` ，因为有三个行包含此值。

   * `INPUT__FILE__NAME LIKE '%.log'` - 告知 Hive，我们只应返回以 .log 结尾的文件中的数据。 此子句将搜索限定为包含数据的 sample.log 文件。

6. 从工具栏中，选择要用于此查询的 **HDInsight 群集** 。 选择“提交”  以 Hive 作业形式运行语句。

   ![“提交”栏](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. “Hive 作业摘要”  将会出现并显示有关正在运行的作业的信息。 在“作业状态”  更改为“已完成”  之前，使用“刷新”  链接刷新作业信息。

   ![显示已完成作业的作业摘要](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. 使用“作业输出”  链接查看此作业的输出。 `[ERROR] 3`，这是此查询返回的值。

### <a name="additional-example"></a>其他示例

此示例依赖于前一步骤中创建的 `log4jLogs` 表。

1. 在“服务器资源管理器”  中，右键单击群集，然后选择“编写 Hive 查询”  。

2. 输入以下 Hive 查询：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句执行以下操作：

   * `CREATE TABLE IF NOT EXISTS`：如果表不存在，则创建表。 由于未使用 `EXTERNAL` 关键字，因此此语句会创建内部表。 内部表存储在 Hive 数据仓库中，由 Hive 管理。

     > [!NOTE]
     > 与 `EXTERNAL` 表不同，删除内部表会同时删除基础数据。

   * `STORED AS ORC`：以优化的行纵栏式 (ORC) 格式存储数据。 ORC 是高度优化且有效的 Hive 数据存储格式。

   * `INSERT OVERWRITE ... SELECT`：从包含 `[ERROR]` 的 `log4jLogs` 表中选择行，然后将数据插入 `errorLogs` 表中。

3. 以**批处理**模式执行查询。

4. 若要验证作业是否已创建表，请使用“服务器资源管理器”  ，然后展开“Azure”   > “HDInsight”  > 你的 HDInsight 群集 >“Hive 数据库”   > “默认值”  。 此时会列出 **errorLogs** 表和 **log4jLogs** 表。

## <a id="nextsteps"></a>后续步骤

可以看到，适用于 Visual Studio 的 HDInsight 工具可以轻松地在 HDInsight 上处理 Hive 查询。

有关 HDInsight 中的 Hive 的一般信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上 Hadoop 的其他使用方法的信息：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关适用于 Visual Studio 的 HDInsight 工具的详细信息：

* [用于 Visual Studio 的 HDInsight 工具入门](apache-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: https://www.azure.cn/pricing/overview/
[azure-member-offers]: https://www.azure.cn/pricing/member-offers/
[azure-trial]: https://www.azure.cn/pricing/1rmb-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: apache-hadoop-connect-excel-power-query.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--Update_Description: update meta data-->