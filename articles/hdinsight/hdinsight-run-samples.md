---
title: 在 HDInsight 中运行 Hadoop 示例 - Azure | Azure
description: 使用所提供的示例开始使用 Azure HDInsight 服务。 在数据群集中使用运行 MapReduce 程序的 PowerShell 脚本。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: article
origin.date: 05/25/2017
ms.date: 04/15/2019
ms.author: v-yiso
ROBOTS: NOINDEX
ms.openlocfilehash: f1ef31047b9353fcde1072f5f92898f6cc743ebb
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004021"
---
# <a name="run-mapreduce-samples-in-windows-based-hdinsight"></a>在基于 Windows 的 HDInsight 中运行 MapReduce 示例
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

为帮助你开始使用 Azure HDInsight 在 Apache Hadoop 群集上运行 MapReduce 作业，我们提供了一组示例。 在创建的每一个 HDInsight 托管群集上都可以使用这些示例。 运行这些示例可熟悉如何使用 Azure PowerShell cmdlet 在 Hadoop 群集上运行作业。

* [**字数统计**][hdinsight-sample-wordcount]：计算单词在文本文件中出现的次数。
* [**C# 流式处理字数统计**][hdinsight-sample-csharp-streaming]：使用 Hadoop 流式处理接口计算单词在文本文件中出现的次数。
* [**PI 估算器**][hdinsight-sample-pi-estimator]：使用统计学方法（拟蒙特卡罗法）来估算 pi 值。
* [**10-GB Graysort**][hdinsight-sample-10gb-graysort]：使用 HDInsight 对 10 GB 文件运行常规用途的 GraySort。 有三个要运行的作业：Teragen 用于生成数据，Terasort 对数据进行排序，而 Teravalidate 确认数据是否已正确排序。

> [!NOTE]
> 可以在附录中找到源代码。

Web 上有许多介绍 Hadoop 相关技术（例如基于 Java 的 MapReduce 编程和流式处理）的其他文档，以及有关 Windows PowerShell 脚本中使用的 cmdlet 的文档。 有关这些资源的详细信息，请参阅：

* [为 HDInsight 中的 Apache Hadoop 开发 Java MapReduce 程序](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [在 HDInsight 中提交 Apache Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Azure HDInsight 简介][hdinsight-introduction]

目前，许多人选择使用 Apache Hive 和 Apache Pig，而不使用 MapReduce。  有关详细信息，请参阅：

* [使用 HDInsight 中的 Apache Hive](hadoop/hdinsight-use-hive.md)
* [在 HDInsight 中使用 Apache Pig](hadoop/hdinsight-use-pig.md)

**先决条件**：

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
* **一个 Azure 订阅**。 请参阅[获取 Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)。
* **一个 HDInsight 群集**。 有关可用于创建这类群集的不同方法的说明，请参阅[在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。
* **配备 Azure PowerShell 的工作站**。

    > [!IMPORTANT]
    > Azure PowerShell 对于使用 Azure Service Manager 管理 HDInsight 资源的支持已**弃用**，会于 2017 年 1 月 1 日删除。 本文档中的步骤使用的是与 Azure 资源管理器兼容的新 HDInsight cmdlet。
    >
    > 请按照[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) 中的步骤安装最新版本的 Azure PowerShell。 如果脚本需要修改后才能使用与 Azure Resource Manager 兼容的新 cmdlet，请参阅[迁移到基于 Azure Resource Manager 的面向 HDInsight 群集的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md)。

## <a name="hdinsight-sample-wordcount"></a>字数统计 - Java
若要提交 MapReduce 项目，请先创建 MapReduce 作业定义。 在作业定义中，指定 MapReduce 程序 jar 文件和 jar 文件的位置（即 wasbs:///example/jars/hadoop-mapreduce-examples.jar）、类名和参数。  Wordcount MapReduce 程序采用两个参数：用于计算字数的源文件以及输出位置。

可以在附录 A 中找到源代码。

有关开发 Java MapReduce 程序的过程，请参阅[开发适用于 HDInsight 中的 Apache Hadoop 的 Java MapReduce 程序](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**提交字数统计 MapReduce 作业**

1. 打开 **Windows PowerShell ISE**。 有关说明，请参阅 [安装和配置 Azure PowerShell][powershell-install-configure]。
2. 粘贴以下 PowerShell 脚本：

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.chinacloudapi.cn'
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    MapReduce 作业生成一个名为 *part-r-00000*的文件，其中包含单词和计数。 该脚本使用 findstr 命令列出包含“there”的所有单词。
3. 设置前 3 个变量，并运行脚本。

## <a name="hdinsight-sample-csharp-streaming"></a>字数统计 - C# 流式处理
Hadoop 向 MapReduce 提供了一个流式处理 API，利用它，可以采用 Java 之外的其他语言来编写映射函数和化简函数。

> [!NOTE]
> 本教程中的步骤仅适用于基于 Windows 的 HDInsight 群集。 有关基于 Linux 的 HDInsight 群集流式处理的示例，请参阅[开发适用于 HDInsight 的 Python 流式处理程序](hadoop/apache-hadoop-streaming-python.md)。

在示例中，映射器和化简器都是可执行的，它们从 [stdin][stdin-stdout-stderr] 读取输入（逐行）并将输出结果发送到 [stdout][stdin-stdout-stderr]。 程序将计算文本中所有单词的数量。

如果为**映射器**指定可执行文件，则当初始化映射器时，每个映射器任务都将启动此可执行文件作为一个单独的进程。 当映射器任务运行时，它将其输入转换为行，并将这些行馈送到进程的 [stdin][stdin-stdout-stderr]。

同时，映射器从进程的 stdout 中收集面向行的输出。 将每行转换为一个键/值对（作为映射程序的输出而收集）。 默认情况下，一行的前缀直至第一个制表符是键，而该行的剩余部分（不包括制表符）是值。 如果行中没有制表符，则整行被视为键，而值为 Null。

如果为**化简器**指定可执行文件，则当初始化化简器时，每个化简器任务都会将此可执行文件作为一个单独的进程启动。 当化简器任务运行时，它将其输入键/值对转换为行，并将这些行馈送到进程的 [stdin][stdin-stdout-stderr] 。

同时，化简器从进程的 [stdout][stdin-stdout-stderr] 中收集面向行的输出。 将每行转换为一个键/值对（作为化简器的输出收集）。 默认情况下，一行的前缀直至第一个制表符是键，而该行的剩余部分（不包括制表符）是值。

**提交 C# 流式处理字数统计作业**

* 请按照字数统计 - Java 中的步骤操作，并将作业定义替换为以下行：

    ```powershell
    $mrJobDefinition = New-AzHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    输出文件应该是：

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>PI 估计器
pi 估计器使用统计学方法（拟蒙特卡罗法）估算 pi 值。 单位平方形内部随机放置的点也落入该平方形内嵌的圆圈内，其概率等于圆圈面积 pi/4。 pi 的值可以从 4R 的值估算出来，其中 R 是圆圈内的点数与正方形内的总点数之比。 点数越多，估算值越准确。

此示例的脚本提交了一个 Hadoop jar 作业，并且设置为使用运行 16 个映射，每个映射通过参数值计算 1 千万个点。 通过更改这些参数值可以提高 pi 的估算值的精度。 例如，pi 采用前 10 位小数时为 3.1415926535。

**提交 pi 估计器作业**

* 请按照字数统计 - Java 中的步骤操作，并将作业定义替换为以下行：

    ```powershell
    $mrJobJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>10-GB Graysort
此示例使用适中的 10 GB 数据，这样它运行时能相对快一点。 它使用由 Owen O'Malley 和 Arun Murthy 开发的 MapReduce 应用程序，此应用程序以 0.578TB/分钟（100 TB 用时 173 分钟）的速率赢得了 2009 年年度常用（“daytona”）TB 级排序基准。 有关这一排序基准和其他排序基准的详细信息，请参阅 [Sortbenchmark](https://sortbenchmark.org/) 站点。

本示例使用三组 MapReduce 程序：

1. **TeraGen** 是一个 MapReduce 程序，可用于生成要排序的数据行。
2. TeraSort 以输入数据为例，使用 MapReduce 将数据排序到总序中。 TeraSort 是 MapReduce 函数的一种标准排序，但自定义的分区程序除外，此分区程序使用 N-1 个抽样键（用于定义每次简化的键范围）的已排序列表。 具体说来，sample[i-1] <= key < sample[i] 的所有键都会发送到化简变量 i。 这样可确保化简变量 i 的输出全都小于化简变量 i+1 的输出。
3. **TeraValidate** 是一个 MapReduce 程序，用于验证输出是否已全局排序。 它在输出目录中为每个文件创建一个映射，每个映射都确保每个键均小于或等于前一个键。 映射函数也会生成每个文件的第一个和最后一个键的记录，并且化简函数可确保文件 i 的第一个键大于文件 i-1 的最后一个键。 任何问题都会报告为包含无次序键的化简的输出结果。

所有三个应用程序使用的输入和输出格式都以正确格式读写文本文件。 化简的输出结果的复制设置为 1，而不是默认值 3，因为基准比赛不要求输出结果数据复制到多个节点上。

此示例要求三个任务，每个任务对应于简介部分介绍的一个 MapReduce 程序：

1. 通过运行 **TeraGen** MapReduce 作业生成要排序的数据。
2. 通过运行 **TeraSort** MapReduce 作业对数据进行排序。
3. 通过运行 **TeraValidate** MapReduce 作业确认数据已正确排序。

**提交作业**

* 按照字数统计 - Java 中的步骤操作，并使用以下作业定义：

    ```powershell
    $teragen = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>后续步骤
从本文和每个示例的相关文章中，了解到如何使用 Azure PowerShell 运行 HDInsight 群集附带的示例。 有关将 Pig、Hive 和 MapReduce 用于 HDInsight 的教程，请参阅以下主题：

* [开始将 Apache Hadoop 与 HDInsight 中的 Apache Hive 配合使用以分析手机使用情况][hdinsight-get-started]
* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用][hdinsight-use-pig]
* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用][hdinsight-use-hive]
* [在 HDInsight 中提交 Apache Hadoop 作业][hdinsight-submit-jobs]

## <a name="appendix-a---the-word-count-source-code"></a>附录 A - 字数统计源代码

```java
package org.apache.hadoop.examples;
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
    extends Mapper<Object, Text, Text, IntWritable>{

private final static IntWritable one = new IntWritable(1);
private Text word = new Text();

public void map(Object key, Text value, Context context
                ) throws IOException, InterruptedException {
    StringTokenizer itr = new StringTokenizer(value.toString());
    while (itr.hasMoreTokens()) {
    word.set(itr.nextToken());
    context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
    extends Reducer<Text,IntWritable,Text,IntWritable> {
private IntWritable result = new IntWritable();

public void reduce(Text key, Iterable<IntWritable> values,
                    Context context
                    ) throws IOException, InterruptedException {
    int sum = 0;
    for (IntWritable val : values) {
    sum += val.get();
    }
    result.set(sum);
    context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
Configuration conf = new Configuration();
String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
if (otherArgs.length != 2) {
    System.err.println("Usage: wordcount <in> <out>");
    System.exit(2);
    }
Job job = new Job(conf, "word count");
job.setJarByClass(WordCount.class);
job.setMapperClass(TokenizerMapper.class);
job.setCombinerClass(IntSumReducer.class);
job.setReducerClass(IntSumReducer.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    }
```

## <a name="appendix-b---the-word-count-streaming-source-code"></a>附录 B - 字数统计流式处理源代码
MapReduce 程序使用 cat.exe 应用程序作为映射接口将文本流式传输到控制台，并使用 wc.exe 应用程序作为化简接口来统计从文档中流式传输的字数。 映射器和化简器都从标准输入流 (stdin) 逐行读取字符，并写入到标准输出流 (stdout)。

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

cat.cs 文件中的映射器代码使用 [StreamReader][streamreader] 对象，将传入流的字符读入到控制台，而控制台使用静态 [Console.Writeline][console-writeline] 方法将流写入标准输出流。

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

wc.cs 文件中的化简器代码使用 [StreamReader][streamreader] 对象，从 cat.exe 映射器输出的标准输入流读取字符。 当它使用 [Console.Writeline][console-writeline] 方法读取字符时，可通过统计位于每个单词末尾的空格和行结束字符的数目来计算单词数量。 然后使用 [Console.Writeline][console-writeline] 方法将总数写入标准输出流。

## <a name="appendix-c---the-pi-estimator-source-code"></a>附录 C - PI 估计器源代码
在下面可以检查包含映射器函数和化简器函数的 pi estimator Java 代码。 映射器程序生成在单位平方形内部随机放置的指定点数，并计算位于圆圈内部的这些点的数目。 化简器程序累计由映射器统计的点数，并根据公式 4R 估算 pi 的值，其中 R 是圆圈内统计的点数与方形内总点数的比率。

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* https://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {true->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>附录 D - 10gb graysort 源代码
本节提供 TeraSort MapReduce 程序的代码以供检查。

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     https://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read partitions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-submit-jobs]: hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:hadoop/apache-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]: hadoop/hdinsight-use-pig.md

[streamreader]: https://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: https://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
<!--Update_Description: change 'wasbs' into 'wasb' and delete a link-->