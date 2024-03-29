---
title: 使用 Power Query 将 Excel 连接到 Hadoop
description: 了解如何利用商业智能组件和使用 Power Query for Excel 访问 HDInsight 上的 Hadoop 中存储的数据。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 05/16/2018
ms.date: 06/10/2019
ms.author: v-yiso
ms.openlocfilehash: 847bd2451ed96d17072d3d2e42d240b827abb26b
ms.sourcegitcommit: 58df3823ad4977539aa7fd578b66e0f03ff6aaee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66424612"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>使用 Power Query 将 Excel 连接到 Apache Hadoop
Microsoft 大数据解决方案的一个关键功能是，将 Microsoft 商业智能 (BI) 组件与 Azure HDInsight 中的 Apache Hadoop 群集相集成。 一个主要示例是能够使用 Microsoft Power Query for Excel 外接程序将 Excel 连接到包含与 Hadoop 群集关联的数据的 Azure 存储帐户。 本文将逐步说明如何设置和使用 Power Query 来查询与 HDInsight 管理的 Hadoop 群集关联的数据。

### <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具有以下项目：

* **HDInsight 群集**。 若要配置 HDInsight 群集，请参阅 [Azure HDInsight 入门][hdinsight-get-started]。
* **工作站** 。
* **Office 2016、Office 2013 Professional Plus、Office 365 ProPlus、Excel 2013 Standalone 或 Office 2010 Professional Plus**。

## <a name="install-power-query"></a>安装 Power Query
Power Query 可以导入已输出的数据，或者导入已由在 HDInsight 群集上运行的 Hadoop 作业生成的数据。

在 Excel 2016 中，Power Query 已集成到“获取并转换”部分下的数据功能区。 对于较旧的 Excel 版本，请从 [Microsoft 下载中心][powerquery-download] 下载 Microsoft Power Query for Excel 并安装它。

## <a name="import-hdinsight-data-into-excel"></a>将 HDInsight 数据导入 Excel
使用用于 Excel 的 Power Query 外接程序，可以轻松地将 HDInsight 群集中的数据导入到 Excel 中，并可以在 Excel 中使用 PowerPivot 和 Power Map 之类的 BI 工具对数据进行检查、分析和显示。

**从 HDInsight 群集导入数据**

1. 打开 Excel。
2. 创建一个新的空白工作簿。
3. 基于 Excel 版本，执行以下步骤：

   - Excel 2016

     - 单击“数据”  菜单，单击“获取并转换数据”  功能区上的“获取数据”  ，单击“从 Azure”  ，然后单击“从 Azure HDInsight (HDFS)”  。

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - 依次单击“Power Query”  菜单、“来自 Azure”  和“来自 Microsoft Azure HDInsight”  。

       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

       **注意：** 如果未看到“Power Query”菜单，请转到“文件” > “选项” > “加载项”，从页面底部的下拉“管理”框中选择“COM 加载项”       。  。

       **注意：** Power Query 还允许通过单击“来自其他源”从 HDFS 中导入数据  。
4. 对于“帐户名称”，请输入与群集相关联的 Azure Blob 存储帐户名称，然后单击“确定”   。 此帐户可以是默认存储帐户或链接的存储帐户。  格式是 *https://&lt;StorageAccountName>.blob.core.chinacloudapi.cn/* 。
5. 对于“帐户密钥”，请输入 Blob 存储帐户的密钥，然后单击“保存”   。 （只需要在首次访问此存储时输入帐户信息。）
6. 在“查询编辑器”左侧的“导航器”窗格中，双击 Blob 存储容器名称  。 默认情况下，该容器名称与群集名称相同。
7. 在“名称”列中找到 HiveSampleData.txt（文件夹路径是 ../hive/warehouse/hivesampletable/），然后单击 HiveSampleData.txt 左侧的“二进制”     。 HiveSampleData.txt 随所有群集提供。 （可选）可使用自己的文件。

    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. 如果需要，可以重命名列名称。 准备就绪后，单击“关闭并加载”  。  数据已加载到工作簿：

    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>后续步骤
在本文中，已了解如何使用 Power Query 将数据从 HDInsight 检索到 Excel 中。 同样地，也可以将来自 HDInsight 的数据检索到 Azure SQL 数据库中。 也可以将数据上传到 HDInsight 中。 要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689

<!--Update_Description: wording update-->