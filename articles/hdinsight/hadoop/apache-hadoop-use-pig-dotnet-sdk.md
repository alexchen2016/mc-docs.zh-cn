---
title: 使用 .NET SDK for Hadoop 运行 Apache Pig 作业 - Azure HDInsight | Azure
description: 了解如何使用 .NET SDK for Hadoop 将 Pig 作业提交到 HDInsight 上的 Hadoop。
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/01/2018
ms.date: 01/14/2019
ms.author: v-yiso
ms.openlocfilehash: 7a8cf98a1a93298e85d4b6a955c550ca4ccfe1b5
ms.sourcegitcommit: 1456ace86f950acc6908f4f5a9c773b93a4d6acc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54029110"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>使用 HDInsight 中用于 Apache Hadoop 的 .NET SDK 运行 Apache Pig 作业

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

了解如何使用 .NET SDK for Apache Hadoop 将 Apache Pig 作业提交到 Azure HDInsight 上的 Hadoop。

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。 Pig 可让你通过为一系列数据转换建模，来创建 MapReduce 操作。 本文档介绍如何使用基本 C# 应用程序将 Pig 作业提交到 HDInsight 群集。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项。

* Azure HDInsight (Hadoop on HDInsight) 群集（基于 Windows 或 Linux）。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* Visual Studio 2012、2013、2015 或 2017。

## <a name="create-the-application"></a>创建应用程序

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。

1. 在 Visual Studio 的“文件”菜单中，选择“新建”，然后选择“项目”。

2. 对于新项目，请键入或选择以下值：

   | 属性 | 值 |
   | ------ | ------ |
   | 类别 | 模板/Visual C#/Windows |
   | 模板 | 控制台应用程序 |
   | Name | SubmitPigJob |

3. 单击“确定”以创建该项目  。

4. 从“工具”菜单中选择“库包管理器”或“Nuget 包管理器”，然后选择“包管理器控制台”。

5. 若要安装 .NET SDK 包，请使用以下命令：

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. 在“解决方案资源管理器”中，双击“Program.cs”将其打开。 将现有代码替换为以下内容。

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.cn";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. 若要启动应用程序，请按 **F5**。

8. 若要退出应用程序，请按 **ENTER**。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 中的 Pig 的信息，请参阅[将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)。

有关如何使用 HDInsight 上的 Hadoop 的详细信息，请参阅以下文档：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.cn/
<!--Update_Description: update metadata-->