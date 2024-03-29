---
title: 配置数据传入复制，以将数据复制到 Azure Database for MariaDB。
description: 本文介绍如何为 Azure Database for MariaDB 设置数据传入复制。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 05/27/2019
ms.openlocfilehash: 5e438a81a064ccbc477433d2907ead134c08bd74
ms.sourcegitcommit: 60169f39663ae62016f918bdfa223c411e249883
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66173232"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>如何配置 Azure Database for MariaDB 的数据传入复制

本文介绍如何通过配置主服务器和副本服务器在 Azure Database for MariaDB 服务中设置“数据传入复制”。 配置“数据传入复制”后，可让你将在本地或虚拟机中运行的主 MariaDB 服务器或其他云提供程序托管的数据库服务中的数据同步到 Azure Database for MariaDB 服务中的副本。 

本文假设读者在 MariaDB 服务器和数据库方面有一定的经验。

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>创建用作副本的 MariaDB 服务器

1. 创建新的 Azure Database for MariaDB 服务器

   创建新的 MariaDB 服务器（例如 “replica.mariadb.database.chinacloudapi.cn”）。 请参阅[使用 Azure 门户创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)，了解如何创建服务器。 在数据传入复制中，此服务器为“副本”服务器。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MariaDB 服务器。
   > 

2. 创建相同的用户帐户和相应的特权

   用户帐户不会从主服务器复制到副本服务器。 如果打算为用户提供副本服务器的访问权限，则需要在此新建的 Azure Database for MariaDB 服务器上创建所有帐户和对应的特权。

## <a name="configure-the-master-server"></a>配置主服务器
以下步骤准备并配置本地或虚拟机中托管的 MariaDB 服务器或其他云提供程序托管的数据库服务，以实现数据传入复制。 此服务器是“数据传入复制”中的“主”服务器。 

1. 启用二进制日志记录

   运行以下命令以检查是否已在主服务器上启用了二进制日志记录： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果返回了包含值“ON”的变量 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin)，则表示已在服务器上启用了二进制日志记录。 

   如果返回了包含值“OFF”的 `log_bin`，请将 my.cnf 文件编辑为 `log_bin=ON` 以启用二进制日志记录，并重启服务器，使更改生效。

2. 主服务器设置

   “数据传入复制”要求参数 `lower_case_table_names` 在主服务器与副本服务器之间保持一致。 在 Azure Database for MariaDB 中，此参数默认为 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 创建新的复制角色并设置权限

   在主服务器上创建一个配置有复制特权的用户帐户。 这可以通过 SQL 命令或 MySQL Workbench 之类的工具来完成。 考虑是否打算使用 SSL 进行复制，因为这需要在创建用户时指定。 请参阅 MariaDB 文档，以了解如何在主服务器上[添加用户帐户](https://mariadb.com/kb/en/library/create-user/)。 

   在以下命令中，创建的新复制角色能够从任何计算机访问主服务器，而不仅仅是从本身托管主服务器的计算机访问主服务器。 可以通过在 create user 命令中指定“syncuser\@'%'”来实现此目的。 请参阅 MariaDB 文档，详细了解如何[指定帐户名称](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL 命令**

   *使用 SSL 复制*

   若要对所有用户连接要求使用 SSL，请使用以下命令创建用户： 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 复制*

   若不要求对所有连接使用 SSL，请使用以下命令创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请从“管理”面板打开“用户和特权”面板。   然后单击“添加帐户”  。 
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在“登录名”字段中键入用户名。  

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   单击“管理角色”面板，然后从“全局特权”列表中选择“复制从属角色”。    然后单击“应用”  ，创建复制角色。

   ![复制从属实例](./media/howto-data-in-replication/replicationslave.png)


4. 将主服务器设置为只读模式

   在开始转储数据库之前，需将服务器置于只读模式。 在只读模式下，主服务器无法处理任何写入事务。 评估对业务的影响，根据需要在非高峰时间计划只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 获取二进制日志文件名和偏移量

   运行 [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 命令，确定当前的二进制日志文件名和偏移量。
    
   ```sql
   show master status;
   ```
   结果应如下所示。 请务必记下二进制文件名，因为需要在后续步骤中用到。

   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>转储并还原主服务器

1. 从主服务器转储所有数据库

   可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](howto-migrate-dump-restore.md)。 不需转储 MySQL 库和测试库。

2. 将主服务器设置为读/写模式

   转储数据库后，将 MariaDB 主服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器

   将转储文件还原到 Azure Database for MariaDB 服务中创建的服务器。 请参阅[转储和还原](howto-migrate-dump-restore.md)，了解如何将转储文件还原到 MariaDB 服务器。 如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的虚拟机。 将转储文件从虚拟机还原到 Azure Database for MariaDB 服务器。

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>链接主服务器和副本服务器以启动“数据传入复制”

1. 设置主服务器

   所有数据传入复制功能都是通过存储过程完成的。 可以在[数据传入复制存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 这些存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。

   若要链接两个服务器并启动复制，请在 Azure DB for MariaDB 服务中登录到目标副本服务器，并将外部实例设置为主服务器。 为此，可在 Azure DB for MariaDB 服务器上使用 `mysql.az_replication_change_master` 存储过程。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host：主服务器的主机名
   - master_user：主服务器的用户名
   - master_password：主服务器的密码
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_ssl_ca：CA 证书的上下文。 如果不使用 SSL，请传入空字符串。
       - 建议以变量形式传入此参数。 有关详细信息，请参阅以下示例。

   **示例**

   *使用 SSL 复制*

   运行以下命令创建变量 `@cert`： 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   在域“companya.com”中托管的主服务器与 Azure Database for MariaDB 中托管的副本服务器之间设置了使用 SSL 进行复制。 将在副本上运行此存储过程。 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *不使用 SSL 复制*

   在域“companya.com”中托管的主服务器与 Azure Database for MariaDB 中托管的副本服务器之间设置了不使用 SSL 进行复制。 将在副本上运行此存储过程。

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. 启动复制

   调用 `mysql.az_replication_start` 存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态

   在副本服务器上调用 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 状态为“yes”，并且 `Seconds_Behind_Master` 的值为“0”，则表示复制正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果其值不为“0”，则表示副本正在处理更新。 

## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止主服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>删除复制关系

若要删除主服务器与副本服务器之间的关系，请使用以下存储过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>跳过复制错误

若要跳过复制错误并允许复制继续，请使用以下存储过程：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure Database for MariaDB 的[数据传入复制](concepts-data-in-replication.md)。
