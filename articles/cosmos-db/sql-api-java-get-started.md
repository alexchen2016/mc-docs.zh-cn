---
title: NoSQL 教程：适用于 Azure Cosmos DB Java SDK 的 SQL API
description: 一个 NoSQL 教程，介绍了如何使用适用于 Azure Cosmos DB 的 SQL API 创建联机数据库和 Java 控制台应用程序。 Azure SQL 是用于 JSON 的 NoSQL 数据库。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
origin.date: 12/22/2018
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 59da25e189e9ebd1484428ee3ff1d6532eb6d332
ms.sourcegitcommit: 153236e4ad63e57ab2ae6ff1d4ca8b83221e3a1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171373"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>NoSQL 教程：构建 SQL API Java 控制台应用程序

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

欢迎使用 NoSQL 教程，了解适用于 Azure Cosmos DB Java SDK 的 SQL API！ 学习本教程后，将拥有一个可创建并查询 Azure Cosmos DB 资源的控制台应用程序。

本文内容：

* 创建并连接到 Azure Cosmos DB 帐户
* 配置 Visual Studio 解决方案
* 创建联机数据库
* 创建集合
* 创建 JSON 文档
* 查询集合
* 创建 JSON 文档
* 查询集合
* 替换文档
* 删除文档
* 删除数据库

现在，让我们开始吧！

## <a name="prerequisites"></a>先决条件
确保具有以下内容：

* 有效的 Azure 帐户。 如果没有，可以注册[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)。
* [Java 开发工具包 (JDK) 7+](https://docs.azure.cn/zh-cn/java/java-supported-jdk-runtime?view=azure-java-stable)。
* [Maven](https://maven.apache.org/download.cgi)。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户
创建 Azure Cosmos DB 帐户。 如果已有一个可用的帐户，可以直接跳到[克隆 GitHub 项目](#GitClone)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[克隆 GitHub 项目](#GitClone)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a name="GitClone"></a>
## <a name="step-2-clone-the-github-project"></a>步骤 2：克隆 GitHub 项目
首先，可以根据 [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started)（Azure Cosmos DB 和 Java 入门）中所述克隆 GitHub 存储库。 例如，在本地目录中运行以下命令，在本地检索示例项目。

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

该目录包含项目的 `pom.xml`，以及含有 Java 源代码的 `src` 文件夹，其中包括 `Program.java`，说明如何使用 Azure Cosmos DB 执行简单的操作，例如创建文档以及查询集合中的数据。 `pom.xml` 依赖于 [Maven 上的 Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

<a name="Connect"></a>
## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a>步骤 3：连接到 Azure Cosmos DB 帐户
接下来，回到 [Azure 门户](https://portal.azure.cn) ，检索终结点和主要主密钥。 Azure Cosmos DB 终结点和主密钥是必需的，可让应用程序知道要连接的对象，使 Azure Cosmos DB 信任应用程序的连接。

在 Azure 门户中，导航到 Azure Cosmos DB 帐户，并单击“密钥”  。 从门户复制 URI，并将其粘贴到 Program.java 文件的 `https://FILLME.documents.azure.cn` 中。 然后从门户中复制“主密钥”并将它粘贴到 `FILLME`。

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.cn",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![NoSQL 教程创建 Java 控制台应用程序时使用的 Azure 门户的屏幕截图。 显示了一个 Azure Cosmos DB 帐户，在“Azure Cosmos DB 帐户”边栏选项卡上突出显示了“ACTIVE”中心、“密钥”按钮，在“密钥”边栏选项卡上突出显示了 URI、主密钥、辅助密钥的值][keys]

## <a name="step-4-create-a-database"></a>步骤 4：创建数据库
可以使用 **DocumentClient** 类的 [createDatabase](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) 方法创建 Azure Cosmos DB [数据库](databases-containers-items.md#azure-cosmos-databases)。 数据库是跨集合分区的 JSON 文档存储的逻辑容器。

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

<a name="CreateColl"></a>
## <a name="step-5-create-a-collection"></a>步骤 5：创建集合
> [!WARNING]
> **createCollection** 创建一个具有保留吞吐量的新集合，它牵涉定价。 有关详细信息，请访问[定价页](https://www.azure.cn/pricing/details/cosmos-db/)。
> 
> 

可以使用 **DocumentClient** 类的 [createCollection](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) 方法创建集合。 集合是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。

    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

<a name="CreateDoc"></a>
## <a name="step-6-create-json-documents"></a>步骤 6：创建 JSON 文档
可以使用 **DocumentClient** 类的 [createDocument](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) 方法创建文档。 文档是用户定义的（任意）JSON 内容。 现在，我们可以插入一个或多个文档。 如果已有要在数据库中存储的数据，则可以使用 Azure Cosmos DB 的[数据迁移工具](import-data.md)将数据导入数据库。

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![说明 NoSQL 教程创建 Java 控制台应用程序所用帐户、联机数据库、集合和文档的层次关系的图表](./media/sql-api-get-started/nosql-tutorial-account-database.png)

<a name="Query"></a>
## <a name="step-7-query-azure-cosmos-db-resources"></a>步骤 7：查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行各种[查询](how-to-sql-query.md)。  以下示例代码展示了如何将 SQL 语法与 [queryDocuments](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments) 方法一起使用来查询 Azure Cosmos DB 中的文档。

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

<a name="ReplaceDocument"></a>
## <a name="step-8-replace-json-document"></a>步骤 8：替换 JSON 文档
Azure Cosmos DB 支持使用 [replaceDocument](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument) 方法更新 JSON 文档。

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

<a name="DeleteDocument"></a>
## <a name="step-9-delete-json-document"></a>步骤 9：删除 JSON 文档
Azure Cosmos DB 支持使用 [deleteDocument](https://docs.azure.cn/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) 方法更新 JSON 文档。  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

<a name="DeleteDatabase"></a>
## <a name="step-10-delete-the-database"></a>步骤 10：删除数据库
删除已创建的数据库将删除该数据库及其所有子资源（集合、文档等）。

    this.client.deleteDatabase("/dbs/familydb", null);

<a name="Run"></a>
## <a name="step-11-run-your-java-console-application-all-together"></a>步骤 11：一起运行 Java 控制台应用程序！
若要从控制台运行应用程序，请导航到项目文件夹，然后使用 Maven 进行编译：

    mvn package

运行 `mvn package` 将从 Maven 下载最新的 Azure Cosmos DB 库，并生成 `GetStarted-0.0.1-SNAPSHOT.jar`。 然后，通过运行以下命令来运行该应用：

    mvn exec:java -D exec.mainClass=GetStarted.Program

祝贺你！ 现已完成本 NoSQL 教程，并构建了一个正常运行的 Java 控制台应用程序！

## <a name="next-steps"></a>后续步骤
* 需要 Java Web 应用教程？ 请参阅[通过 Java 构建使用 Azure Cosmos DB 的 Web 应用程序](sql-api-java-application.md)。
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中对示例数据集运行查询。

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png

<!-- Update_Description: update meta properties, update link -->