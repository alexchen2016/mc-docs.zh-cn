---
title: 了解如何使用 Apache Hadoop 沙盒 - 模拟器 - Azure HDInsight
description: '若要开始了解如何使用 Apache Hadoop 生态系统，可以在 Azure 虚拟机上设置 Hortonworks 提供的 Hadoop 沙盒。 '
keywords: hadoop emulator,hadoop 沙盒
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: ''
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/29/2019
ms.date: 06/24/2019
ms.author: v-yiso
ms.openlocfilehash: 5fd455fe37f6eb4c7ec000b01374089bc2da1f68
ms.sourcegitcommit: e77582e79df32272e64c6765fdb3613241671c20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135755"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>开始使用 Apache Hadoop 沙盒，它是虚拟机上的模拟器

了解如何在虚拟机上安装 Hortonworks 提供的 Apache Hadoop 沙盒，以了解 Hadoop 生态系统。 该沙盒提供一个本地开发环境，让用户了解 Hadoop、Hadoop 分布式文件系统 (HDFS) 和作业提交内容。 熟悉 Hadoop 之后，便可以开始在 Azure 中使用 Hadoop 创建 HDInsight 群集。 有关如何入门的详细信息，请参阅[在 HDInsight 中开始使用 Hadoop](apache-hadoop-linux-tutorial-get-started.md)。

## <a name="prerequisites"></a>先决条件
* [Oracle VirtualBox](https://www.virtualbox.org/)。 从 [此处](https://www.virtualbox.org/wiki/Downloads)下载并安装它。



## <a name="download-and-install-the-virtual-machine"></a>下载并安装虚拟机
1. 浏览到 [Cloudera 下载](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)。

2. 单击“选择安装类型”  下的 **VIRTUALBOX**，在 VM 上下载最新的 Hortonworks 沙盒。 登录或填写产品兴趣表。

1. 单击按钮“HDP 沙盒(最新)”  开始下载。

有关设置沙盒的说明，请参阅[沙盒部署和安装指南](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)。

若要下载旧版本的 HDP 沙盒，请参阅“旧版本”  下的链接。

## <a name="start-the-virtual-machine"></a>启动虚拟机

1. 打开 Oracle VM VirtualBox。
2. 在“文件”菜单上，单击“导入设备”，然后指定 Hortonworks 沙盒映像   。
1. 选择 Hortonworks 沙盒，依次单击“启动”  、“正常启动”  。 虚拟机完成启动过程后，显示登录说明。
   
    ![正常启动](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. 打开 Web 浏览器并导航到显示的 URL（通常是 http://127.0.0.1:8888) 。

## <a name="set-sandbox-passwords"></a>设置沙盒密码

1. 在“Hortonworks 沙盒”页的“开始”  步骤中，选择“查看高级选项”  。 使用此页上的信息通过 SSH 登录到沙盒。 使用提供的名称和密码。

   > [!NOTE]
   > 如果未安装 SSH 客户端，可以使用虚拟机在 **http://localhost:4200/** 上提供的基于 Web 的 SSH。
   > 

    首次使用 SSH 建立连接时，系统会提示更改 root 帐户的密码。 输入新密码，在使用 SSH 登录时将使用该密码。

2. 登录后，请输入以下命令：

        ambari-admin-password-reset

    出现提示时，请提供 Ambari 管理员帐户的密码。 访问 Ambari Web UI 时要用到此密码。

## <a name="use-hive-commands"></a>使用 Hive 命令

1. 与沙盒建立 SSH 连接后，使用以下命令启动 Hive shell：

        hive
2. 启动 shell 后，使用以下命令查看随沙盒一起提供的表：

        show tables;
3. 使用以下命令检索 `sample_07` 表中的 10 行数据：

        select * from sample_07 limit 10;

## <a name="next-steps"></a>后续步骤
* [了解如何将 Visual Studio 与 Hortonworks 沙盒配合使用](../hdinsight-hadoop-emulator-visual-studio.md)
* [了解 Hortonworks 沙盒的重要知识](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 教程 - HDP 入门](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!--Update_Description: update meta data-->