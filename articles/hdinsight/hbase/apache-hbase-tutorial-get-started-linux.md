---
title: HDInsight 上的 HBase 入门示例
description: 按照此 Apache HBase 示例，开始在 HDInsight 上使用 Hadoop。 从 HBase shell 创建表，并使用 Hive 查询这些表。
keywords: hbasecommand,hbase 示例
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
origin.date: 02/22/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: eb94ec5f7d43658b2381535ee9006687115b50e7
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003853"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>HDInsight 中的 Apache HBase 入门示例

了解如何使用 [Apache Hive](https://hive.apache.org/) 在 HDInsight 中创建 [Apache HBase](https://hbase.apache.org/) 群集、创建 HBase 表和查询表。  有关 HBase 的一般信息，请参阅 [HDInsight HBase 概述][hdinsight-hbase-overview]。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>先决条件
开始使用本 HBase 示例前，必须具有以下项目：

* **一个 Azure 订阅**。 请参阅[获取 Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)。
* [安全外壳 (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 
* [curl](https://curl.haxx.se/download.html)。

## <a name="create-apache-hbase-cluster"></a>创建 Apache HBase 群集
以下过程使用 Azure 资源管理器模板创建 HBase 群集以及相关的默认 Azure 存储帐户。 若要了解该过程与其他群集创建方法中使用的参数，请参阅 [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。 有关使用 Data Lake Storage Gen2 的详细信息，请参阅[快速入门：在 HDInsight 中设置群集](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。

1. 单击下面的图像可在 Azure 门户中打开模板。 该模板位于 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)中。

    <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    >[!NOTE]
    > 必须修改从 GitHub 存储库“azure-quickstart-templates”下载的模板，以适应 Azure 中国云环境。 例如，替换一些终结点 - 将“blob.core.chinacloudapi.cn”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”；将允许的位置更改为“中国北部”和“中国东部”；将 HDInsight Linux 版本更改为 Azure 中国区支持的版本：3.5。

2. 在“自定义部署”  边栏选项卡中，输入以下信息：

   * **订阅**：选择用于创建群集的 Azure 订阅。
   * **资源组**：创建 Azure 资源管理组，或使用现有的组。
   * **位置**：指定资源组的位置。 
   * **ClusterName**：输入 HBase 群集的名称。
   * **群集登录名和密码**：默认登录名为“admin”。
   * **SSH 用户名和密码**：默认用户名为“sshuser”。  可以重命名它。

     其他参数是可选的。  

     每个群集都有一个 Azure 存储帐户依赖项。 删除群集后，数据将保留在存储帐户中。 群集的默认存储帐户名为群集名称后接“store”。 该名称已在模板 variables 节中硬编码。
3. 选择“我同意上述条款和条件”，并单击“购买”。 创建群集大约需要 20 分钟时间。

> [!NOTE]
> 删除 HBase 群集后，可使用同一默认 Blob 容器创建另一 HBase 群集。 新群集会选取在原始群集中创建的 HBase 表。 为了避免不一致，建议在删除群集之前先禁用 HBase 表。
> 
> 

## <a name="create-tables-and-insert-data"></a>创建表和插入数据
可以使用 SSH 连接到 HBase 群集，并使用 [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) 来创建 HBase 表以及插入和查询数据。 有关详细信息，请参阅 [将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

对大多数用户而言，数据以表格形式显示：

![HDInsight HBase 表格数据][img-hbase-sample-data-tabular]

在 HBase（[Cloud BigTable](https://cloud.google.com/bigtable/) 的一种实现）中，相同的数据看起来类似于：

![HDInsight HBase BigTable 数据][img-hbase-sample-data-bigtable]

**使用 HBase shell**

1. 从 SSH 运行以下 HBase 命令：

    ```bash
    hbase shell
    ```

2. 创建包含两个列系列的 HBase：

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. 插入一些数据：

    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase shell][img-hbase-shell]
4. 获取单个行

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    将会看到与使用扫描命令相同的结果，因为只有一个行。
   
    有关 HBase 表架构的详细信息，请参阅 [Apache HBase 架构设计简介][hbase-schema]。 有关 HBase 命令的详细信息，请参阅 [Apache HBase 参考指南][hbase-quick-start]。
5. 退出 shell

    ```hbaseshell
    exit
    ```

**在联系人 HBase 表中批量加载数据**

HBase 提供了多种方法用于将数据载入表中。  有关详细信息，请参阅 [批量加载](https://hbase.apache.org/book.html#arch.bulk.load)。

可在公共 Blob 容器 <em>wasb://hbasecontacts@hditutorialdata.blob.core.chinacloudapi.cn/contacts.txt</em> 中找到示例数据文件。  该数据文件的内容为：

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

可以选择创建一个文本文件并将该文件上传到自己的存储帐户。 有关说明，请参阅[在 HDInsight 中为 Apache Hadoop 作业上传数据][hdinsight-upload-data]。

> [!NOTE]
> 此过程使用在上一个过程中创建的“联系人”HBase 表。

1. 从 SSH 运行以下命令，将数据文件转换成 StoreFiles 并将其存储在 Dimporttsv.bulk.output 指定的相对路径。  如果在 HBase Shell 中操作，请使用退出命令退出。

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.chinacloudapi.cn/contacts.txt
    ```

2. 运行以下命令，将数据从 /example/data/storeDataFileOutput 上传到 HBase 表：

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. 可以打开 HBase Shell，并使用扫描命令来列出表内容。

## <a name="use-apache-hive-to-query-apache-hbase"></a>使用 Apache Hive 查询 Apache HBase

可以使用 [Apache Hive](https://hive.apache.org/) 查询 HBase 表中的数据。 本部分将创建要映射到 HBase 表的 Hive 表，并使用该表来查询 HBase 表中的数据。

1. 打开 **PuTTY**并连接到群集。  参阅前一过程中的说明。
2. 在 SSH 会话中，使用以下命令启动 Beeline：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    有关 Beeline 的详细信息，请参阅[通过 Beeline 将 Hive 与 HDInsight 中的 Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-beeline.md)。
       
3. 运行以下 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 脚本，创建映射到 HBase 表的 Hive 表。 确保已创建本教程中前面引用的示例表，方法是在运行此语句前使用 HBase shell。

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. 运行以下 HiveQL 脚本，以查询 HBase 表中的数据：

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>通过 Curl 使用 HBase REST API

REST API 通过 [基本身份验证](https://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 始终应该使用安全 HTTP (HTTPS) 来发出请求，确保安全地将凭据发送到服务器。

2. 使用以下命令列出现有的 HBase 表：

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.cn/hbaserest/
    ```

3. 使用以下命令创建包含两个列系列的新 HBase 表：

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.cn/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    架构以 JSON 格式提供。
4. 使用以下命令插入一些数据：

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.cn/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    必须使用 base64 来为 -d 参数中指定的值编码。 在此示例中：

   * MTAwMA==:1000
   * UGVyc29uYWw6TmFtZQ==:Personal:Name
   * Sm9obiBEb2xl:John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) 允许插入多个（批处理）值。
5. 使用以下命令获取行：

    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.cn/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

有关 HBase Rest 的详细信息，请参阅 [Apache HBase 参考指南](https://hbase.apache.org/book.html#_rest)。

> [!NOTE]
> Thrift 不受 HDInsight 中的 HBase 支持。
>
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 此外，还必须使用群集名称作为用来向服务器发送请求的统一资源标识符 (URI) 的一部分：
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.cn/templeton/v1/status
>   
>    应会收到类似于以下响应的响应：
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>检查群集状态
HDInsight 中的 HBase 随附了一个 Web UI 用于监视群集。 使用该 Web UI 可以请求有关区域的统计或信息。

**访问 HBase Master UI**

1. 通过 https://&lt;群集名称>.azurehdinsight.cn 登录到 Ambari Web UI。
2. 在左侧菜单中，单击“HBase”  。
3. 在页面顶部单击“快速链接”，指向活动 Zookeeper 节点链接，并单击“HBase Master UI”。  该 UI 会在另一个浏览器标签页中打开：

   ![HDInsight HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI 包含以下部分：

   - 区域服务器
   - 备份主机
   - 表
   - 任务
   - 软件属性

## <a name="delete-the-cluster"></a>删除群集
为了避免不一致，建议在删除群集之前先禁用 HBase 表。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](../hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>后续步骤
本文已介绍如何创建 Apache HBase 群集、如何创建表以及如何从 HBase shell 查看这些表中的数据。 此外，学习了如何对 HBase 表中的数据使用 Hive 查询，以及如何使用 HBase C# REST API 创建 HBase 表并从该表中检索数据。

若要了解更多信息，请参阅以下文章：

* [HDInsight HBase 概述][hdinsight-hbase-overview]：Apache HBase 是构建于 Apache Hadoop 上的 Apache 开源 NoSQL 数据库，用于为大量非结构化和半结构化数据提供随机访问和高度一致性。

[hdinsight-manage-portal]: ../hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://www.azure.cn/pricing/overview/
[azure-member-offers]: https://www.azure.cn/pricing/member-offers/
[azure-trial]: https://www.azure.cn/pricing/1rmb-trial/
[azure-portal]: https://portal.azure.cn/
[azure-create-storageaccount]: /storage/common/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
