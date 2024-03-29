---
title: 实现地理分散的 Azure SQL 数据库解决方案 | Microsoft Docs
description: 了解如何配置 Azure SQL 数据库和应用程序，以便故障转移到复制的数据库，以及进行测试性故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 03/12/2019
ms.date: 04/08/2019
ms.openlocfilehash: fe9802894b965ce9be4cba3d8c14060f3acddaf5
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003486"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>教程：实现地理分散的数据库

配置故障转移到远程区域所需的 Azure SQL 数据库和应用程序，然后测试故障转移计划。 你将学习如何执行以下操作：

> [!div class="checklist"]
> - 创建[故障转移组](sql-database-auto-failover-group.md)
> - 运行 Java 应用程序以查询 Azure SQL 数据库
> - 测试故障转移

如果没有 Azure 订阅，可在开始前[创建一个 1 元试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要完成本教程，请确保已安装以下项目：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)。
- Azure SQL 数据库。 若要创建一个，请使用：
  - [门户](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > 本教程使用 *AdventureWorksLT* 示例数据库。

- Java 和 Maven，请参阅 [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/)（使用 SQL Server 生成应用），突出显示 **Java**，选择环境，然后按步骤操作。

> [!IMPORTANT]
> 请务必设置防火墙规则，以便使用要在其上执行本教程中步骤的计算机的公共 IP 地址。 数据库级防火墙规则会自动复制到辅助服务器。
>
> 有关信息，请参阅[创建数据库级防火墙规则](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)；若要确定计算机的用于服务器级防火墙规则的 IP 地址，请参阅[创建服务器级防火墙](sql-database-server-level-firewall-rule.md)。  

## <a name="create-a-failover-group"></a>创建故障转移组

使用 Azure PowerShell 创建[故障转移组](sql-database-auto-failover-group.md)，以便在现有的 Azure SQL Server 和另一区域的全新 Azure SQL Server 之间进行故障转移。 然后，将示例数据库添加到故障转移组。

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

若要创建故障转移组，请运行以下脚本：

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       -FailoverGroupName $myfailovergroupname `
       -FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

异地复制设置也可在 Azure 门户中更改，方法是：选择数据库，然后选择“设置” > “异地复制”。

![异地复制设置](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>运行示例项目

1. 在控制台中，使用以下命令创建一个 Maven 项目：

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. 键入 **Y**，然后按 **Enter**。

1. 将目录切换到新项目。

   ```bash
   cd SqlDbSample
   ```

1. 使用最常用的编辑器，打开项目文件夹中的 *pom.xml* 文件。

1. 通过添加下面的 `dependency` 节来添加 Microsoft JDBC Driver for SQL Server 依赖项。 此依赖项必须粘贴在更大的 `dependencies` 节中。

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. 将 `properties` 节添加到 `dependencies` 节之后，指定 Java 版本：

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 将 `build` 节添加到 `properties` 节之后，为清单文件提供支持：

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. 保存并关闭 *pom.xml* 文件。

1. 打开 ..\SqlDbSample\src\main\java\com\sqldbsamples 中的 *App.java* 文件，将内容替换为以下代码：

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.chinacloudapi.cn:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.chinacloudapi.cn:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. 保存并关闭 *App.java* 文件。

1. 在命令控制台中运行以下命令：

   ```bash
   mvn package
   ```

1. 启动应用程序。如果不手动停止，应用程序将运行大约 1 小时，让你有时间运行故障转移测试。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>测试故障转移

运行以下脚本来模拟故障转移，观察应用程序结果。 注意在数据库迁移过程中某些插入和选择操作是如何失败的。

也可使用以下命令，检查灾难恢复服务器在测试过程中的角色：

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

若要测试某个故障转移，请执行以下操作：

1. 启动对故障转移组的手动故障转移：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. 将故障转移组还原到主服务器：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

