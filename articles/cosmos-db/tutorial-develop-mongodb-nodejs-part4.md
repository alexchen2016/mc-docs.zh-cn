---
title: 使用 Azure Cosmos DB 的 API for MongoDB 创建 Angular 应用 - 创建 Cosmos 帐户
titleSuffix: Azure Cosmos DB
description: 本教程系列的第 4 部分，介绍如何通过 Angular 和 Node 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与用于 MongoDB 的 API 完全相同
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.author: v-yeche
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 3ebd1dc22e25a3eabd5ed3cac4c65ed707cb7811
ms.sourcegitcommit: c5646ca7d1b4b19c2cb9136ce8c887e7fcf3a990
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2019
ms.locfileid: "58004690"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>使用 Azure Cosmos DB 的 API for MongoDB 创建 Angular 应用 - 创建 Cosmos 帐户

本教程包含多个部分，演示了如何通过 Express 和 Angular 创建以 Node.js 编写的新应用，然后将其连接到[使用 Cosmos DB 的 API for MongoDB 配置的 Cosmos 帐户](mongodb-introduction.md)。

本教程的第 4 部分基于[第 3 部分](tutorial-develop-mongodb-nodejs-part3.md)，涵盖以下任务：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 资源组
> * 使用 Azure CLI 创建 Cosmos 帐户

<!-- Not Available ## Video walkthrough-->

## <a name="prerequisites"></a>先决条件

开始教程的此部分之前，请确保已完成教程[第 3 部分](tutorial-develop-mongodb-nodejs-part3.md)的步骤。 

在教程的此部分，可以使用本地安装的 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

<!--MOONCAKE: Not Available on Azure Cloud Shell-->

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用 [`az cosmosdb create`](https://docs.azure.cn/zh-cn/cli/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) 命令创建 Azure Cosmos DB 帐户。

```azurecli
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* 若要将自己的唯一 Azure Cosmos DB 帐户名称用于 `<cosmosdb-name>`，该名称在 Azure 的所有 Azure Cosmos DB 帐户名称中必须是唯一的。
* `--kind MongoDB` 设置允许 Azure Cosmos DB 进行 MongoDB 客户端连接。

完成该命令可能需要一到两分钟的时间。 完成后，Terminal 窗口会显示新数据库的相关信息。 

创建 Azure Cosmos DB 帐户以后，请执行以下操作：
1. 打开新的浏览器窗口，访问 [https://portal.azure.cn](https://portal.azure.cn)
1. 单击左侧栏中的 Azure Cosmos DB 徽标 ![Azure 门户中的 Azure Cosmos DB 图标](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) ，然后就会显示你的所有 Azure Cosmos DB。
1. 单击刚创建的 Azure Cosmos DB 帐户，选择“概览”选项卡，向下滚动，以便查看数据库所在的映射。 

    ![Azure 门户中的新 Azure Cosmos DB 帐户](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. 在左侧导航中向下滚动，单击“以多区域方式复制数据”选项卡。此时会显示一个映射，可以在其中看到允许将数据复制到其中的不同区域。 例如，可以单击“中国北部”或“中国东部”，并将数据复制到“中国”。 若要详细了解多区域复制，可参阅[如何使用 Azure Cosmos DB 在多个区域分配数据](distribute-data-globally.md)。 至于现在，我们只需保留这一个实例，这样在需要复制时，我们就知道如何去做。

    ![Azure 门户中的新 Azure Cosmos DB 帐户](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>后续步骤

在本教程的此部分，你已完成以下操作：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 资源组
> * 使用 Azure CLI 创建 Azure Cosmos DB 帐户

你可以转到本教程的下一部分，了解如何使用 Mongoose 将 Azure Cosmos DB 连接到应用。

> [!div class="nextstepaction"]
> [使用 Mongoose 连接到 Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)

<!-- Update_Description: update meta properties, wording update, update link -->