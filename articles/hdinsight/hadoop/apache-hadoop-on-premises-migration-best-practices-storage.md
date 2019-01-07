---
title: 将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 存储最佳做法
description: 了解有关将本地 Hadoop 群集迁移到 Azure HDInsight 的存储最佳做法。
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 10/25/2018
ms.date: 01/14/2019
ms.author: v-yiso
ms.openlocfilehash: e25d8a4926438f17540f58ebfda4fcb1b6bb2210
ms.sourcegitcommit: 1456ace86f950acc6908f4f5a9c773b93a4d6acc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54029214"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 存储最佳做法

本文提供有关 Azure HDInsight 系统数据存储的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>为 HDInsight 群集选择合适的存储系统

可在 Azure 存储或 Azure Data Lake Storage 重新创建本地 Apache Hadoop 文件系统 (HDFS) 目录结构。 可安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。 这两种服务既可以用作默认文件系统，也可以用作 HDInsight 群集的附加文件系统。 HDInsight 群集和存储帐户必须位于同一区域。

### <a name="azure-storage"></a>Azure 存储

HDInsight 群集可将 Azure 存储中的 blob 容器用作默认文件系统或其他文件系统。 支持将标准层存储帐户与 HDInsight 群集配合使用。 不支持高级层。 默认的 Blob 容器存储群集特定的信息，如作业历史记录和日志。 不支持将单个 blob 容器共享为多个群集的默认文件系统。

创建过程中定义的存储帐户及其对应的密钥存储在群集节点上的 `%HADOOP_HOME%/conf/core-site.xml` 中。 也可在 Ambari UI 中的 HDFS 配置中的“自定义核心站点”部分下访问它们。 默认情况下将加密存储帐户密钥，并且使用自定义解密脚本在将密钥传递给 Hadoop 守护程序之前解密密钥。 很多作业（包括 Hive、MapReduce、Hadoop Streaming 和 Pig）都带有存储帐户和元数据的说明。

可对 Azure 存储进行异地复制。 尽管异地复制可提供地理恢复和数据冗余功能，但针对异地复制位置的故障转移将大大影响性能，并且可能会产生额外成本。 建议仅在数据的价值值得你支付额外成本时才选择适当的地理复制。

可以使用以下格式之一访问存储在 Azure 存储中的数据：

|数据访问格式 |说明 |
|---|---|
|`wasb:///`|使用未加密通信访问默认存储。|
|`wasbs:///`|使用加密通信访问默认存储。|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|与非默认存储帐户通信时使用。 |


[Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)列出了 Azure 存储帐户的当前限制。 如果应用程序的需求超过单个存储帐户的伸缩性目标，则在构建时让应用程序使用多个存储帐户，并将数据对象分布到这些存储帐户中。

[Azure 存储分析](../../storage/storage-analytics.md)  提供了所有存储服务的指标，可配置 Azure 门户来收集这些指标，以便通过图表直观显示。 可以创建警报，以便在达到存储资源指标的阈值时收到通知。

Azure 存储现提供 [Blob 对象软删除](../../storage/blobs/storage-blob-soft-delete.md)，以便在应用程序或其他存储帐户用户意外修改或删除数据后恢复数据。

可创建 [blob 快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)。 快照是在某一时间点拍摄的只读版本的 blob，这是一种备份 blob 的方法。 在创建快照后，可以读取、复制或删除该快照，但无法对其进行修改。

> [!Note]
> 对于没有“wasbs”证书的旧版本地 Hadoop 发行版，需要将其导入 Java 信任存储区。

可以使用以下方法将证书导入 Java 信任存储区：

将 Azure Blob SSL 证书下载到文件

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

将上述文件导入所有节点上的 Java 信任存储区

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

验证添加的证书是否位于信任存储区

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

有关详细信息，请参阅以下文章：

- [将 Azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md)
- [Azure 存储伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)
- [Microsoft Azure 存储性能和可伸缩性清单](../../storage/common/storage-performance-checklist.md)
- [监视、诊断和排查 Microsoft Azure 存储问题](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [在 Azure 门户中监视存储帐户](../../storage/common/storage-monitor-storage-account.md)





## <a name="protect-azure-storage-key-visibility-within-the-on-premises-hadoop-cluster-configuration"></a>在本地 Hadoop 群集配置中保护 Azure 存储密钥可见性

添加到 Hadoop 配置文件的 Azure 存储密钥在本地 HDFS 和 Azure Blob 存储之间建立连接。 可以使用 Hadoop 凭据提供程序框架对这些密钥进行加密来保护这些密钥。 加密后，可以安全地存储和访问它们。

**预配凭据：**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**将上述提供程序路径添加到 core-site.xml 或自定义核心站点下的 Ambari 配置：**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> 也可将提供程序路径属性添加到 distcp 命令行，而不是将密钥存储在 core-site.xml 的群集级别，如下所示：

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>使用 SAS 限制 Azure 存储数据访问

默认情况下，HDInsight 对群集关联的 Azure 存储帐户中的数据拥有完全访问权限。 Blob 容器上的共享访问签名 (SAS) 可用于限制对数据的访问，例如为用户提供对数据的只读访问权限。

### <a name="using-the-sas-token-created-with-python"></a>使用通过 python 创建的 SAS 令牌

1. 打开 [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) 文件并更改以下值：

    |令牌属性|说明|
    |---|---|
    |policy_name|要创建的存储策略所用的名称。|
    |storage_account_name|存储帐户的名称。|
    |storage_account_key|存储帐户的密钥。|
    |storage_container_name|想要限制访问的存储帐户中的容器。|
    |example_file_path|上传到容器的文件的路径。|

2. SASToken.py 文件附带 `ContainerPermissions.READ + ContainerPermissions.LIST` 权限，可以根据用例进行调整。

3. 按如下所示执行脚本：`python SASToken.py`

4. 脚本完成后，会显示如以下文本所示的 SAS 令牌：`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. 要限制对具有共享访问签名的容器的访问，请在“Ambari HDFS 配置高级自定义”核心站点的“添加”属性下为群集的核心站点配置添加自定义条目。

6. 将以下值用于“密钥”和“值”字段 ****  **** ：

    **密钥**：`fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **值**：Python 应用程序从上面的步骤 4 返回的 SAS 密钥。

7. 单击“添加”按钮以保存此密钥和值，并单击“保存”按钮以保存配置更改 ****  **** 。 出现提示时，请添加更改的说明（例如，“添加 SAS 存储访问”），并单击“保存” ****。

8. 在 Ambari Web UI 中，从左侧的列表中选择“HDFS”，并从右侧的“服务操作”下拉列表中选择“重启所有受影响项” **** 。 出现提示时，选择“确认全部重启” ****。

9. 对 MapReduce2 和 YARN 重复此过程。

关于在 Azure 中使用 SAS 令牌，有三个重要事项需要记住：

1. 使用“READ + LIST”权限创建 SAS 令牌时，使用该 SAS 令牌访问 Blob 容器的用户将无法“写入和删除”数据。 使用该 SAS 令牌访问 Blob 容器并尝试写入或删除操作的用户将收到类似 `"This request is not authorized to perform this operation"` 的消息。

2. 当使用 `READ + LIST + WRITE` 权限生成 SAS 令牌（仅限 `DELETE`）时，`hadoop fs -put` 等命令首先写入 `\_COPYING\_` 文件，然后尝试重命名该文件。 此 HDFS 操作映射到 WASB 的 `copy+delete`。 由于未提供 `DELETE` 权限，因此“put”将失败。 `\_COPYING\_` 操作是一个 Hadoop 功能，旨在提供一些并发控制。 目前，没有办法仅限制“DELETE”操作而不影响“WRITE”操作。

3. 遗憾的是，hadoop 凭证提供程序和解密密钥提供程序 (ShellDecryptionKeyProvider) 当前不能与 SAS 令牌配合使用，因此目前无法对其可见性提供保护。

有关详细信息，请参阅[使用 Azure 存储共享访问签名来限制访问 HDInsight 中的数据](../hdinsight-storage-sharedaccesssignature-permissions.md)

## <a name="use-data-encryption-and-replication"></a>使用数据加密和复制

所有写入 Azure 存储的数据都使用 [存储服务加密 (SSE)](../../storage/common/storage-service-encryption.md) 进行自动加密。 始终复制 Azure 存储帐户中的数据以实现高可用性。 创建存储帐户时，可以选择以下复制选项之一：

- [本地冗余存储 (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [异地冗余存储 (GRS)](../../storage/common/storage-redundancy-grs.md)
- [读取访问异地冗余存储 (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)


有关详细信息，请参阅以下文章：

- [Azure 存储复制](../../storage/common/storage-redundancy.md)
## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>将其他 Azure 存储帐户附加到该群集

在 HDInsight 创建过程中，将选择 Azure 存储帐户或者 Azure Data Lake Storage 作为默认文件系统。 除了此默认存储帐户，在群集创建过程中或群集创建完成后，还可以从同一 Azure 订阅或不同 Azure 订阅添加其他存储帐户。

可以通过以下方式之一添加其他存储帐户：
- 在“Ambari HDFS 配置高级自定义”核心站点，添加存储帐户名称和密钥并重启服务
- 通过传递存储帐户名称和密钥，使用[脚本操作](../hdinsight-hadoop-add-storage.md)

> [!Note]
> 在有效的用例中，可以通过向  [Azure 支持](https://azure.microsoft.com/support/faq/)发出的请求来增加对 Azure 存储的限制。

有关详细信息，请参阅以下文章：
- [将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md)
- [将其他 Azure 存储帐户附加到该群集](https://blogs.msdn.microsoft.com/ashish/2016/08/25/hdinsight-attach-additional-azure-storage-accounts/)

## <a name="next-steps"></a>后续步骤

阅读本系列教程的下一篇文章：

- [有关从本地迁移到 Azure HDInsight Hadoop 的数据迁移最佳做法](apache-hadoop-on-premises-migration-best-practices-data-migration.md)