---
title: 使用 Java 查询 Azure SQL 数据库 | Microsoft Docs
description: 介绍如何使用 Java 创建连接到 Azure SQL 数据库的程序并使用 T-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: WenJason
ms.author: v-jay
ms.reviewer: v-masebo
manager: digimobile
origin.date: 03/25/2019
ms.date: 04/15/2019
ms.openlocfilehash: 88858c97a3db8a1b3f634bceb6892f25a32eaa7e
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529334"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>快速入门：使用 Java 查询 Azure SQL 数据库

本文演示了如何使用 [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 连接到 Azure SQL 数据库。 然后即可使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成此示例，请确保具备以下先决条件：

- Azure SQL 数据库。 可以根据下述快速入门中的一个的说明在 Azure SQL 数据库中创建数据库，然后对其进行配置：

  || 单一数据库 |
  |:--- |:--- |
  | 创建| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | 配置 | [服务器级别 IP 防火墙规则](sql-database-server-level-firewall-rule.md)|
  |加载数据|根据快速入门加载的 Adventure Works|
  |||

- 已为操作系统安装与 Java 相关的软件：

  - **MacOS**：安装 Homebrew 和 Java，然后安装 Maven。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)。

  - **Ubuntu**：安装 Java 和 Java 开发工具包，然后安装 Maven。 请参阅[步骤 1.2、1.3 和 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)。

  - **Windows**：安装 Java，然后安装 Maven。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.cn/)。

2. 导航到“SQL 数据库”页面。

3. 在“概述”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。 

## <a name="create-the-project"></a>创建项目

1. 从命令提示符创建名为 sqltest 的新 Maven 项目。

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 将文件夹更改为 *sqltest*，然后使用喜欢的文本编辑器打开 pom.xml。 使用以下代码，将 **Microsoft JDBC Driver for SQL Server** 添加到项目的依赖项。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 另请在 pom.xml 中向项目添加以下属性。 如果没有 properties 节，可以将其添加到 dependencies 后面。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 保存并关闭 pom.xml。

## <a name="add-code-to-query-database"></a>添加用于查询数据库的代码

1. 此时，你应该在 Maven 项目中有了一个名为 App.java 的文件，其位置为：

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. 打开该文件并将其内容替换为以下代码。 然后，为服务器、数据库、用户和密码添加相应的值。

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.chinacloudapi.cn"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > 代码示例使用适用于 Azure SQL 的 **AdventureWorksLT** 示例数据库。

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此应用。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 验证是否返回了前 20 行，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)  

- [用于 SQL Server 的 Microsoft JDBC 驱动程序](https://github.com/microsoft/mssql-jdbc)  

- [报告问题/提出问题](https://github.com/microsoft/mssql-jdbc/issues)  
