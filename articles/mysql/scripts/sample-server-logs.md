---
title: Azure CLI 脚本 - 下载 Azure Database for MySQL 中的服务器日志
description: 此示例 Azure CLI 脚本演示如何启用和下载 Azure Database for MySQL 服务器的服务器日志。
services: mysql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
origin.date: 02/28/2018
ms.date: 08/13/2018
ms.openlocfilehash: 1a8624ed39bb61fd85abefab067cf967c8d06750
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52661498"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 启用和下载 Azure Database for MySQL 服务器的服务器慢查询日志

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql/)。

此示例 CLI 脚本可启用和下载单个 Azure Database for MySQL 服务器的慢查询日志。 本文需要 Azure CLI 2.0 或更高版本。 通过运行 `az --version` 来查看版本。 请参阅[安装 Azure CLI 2.0]( /cli/install-azure-cli)，了解如何安装或升级 Azure CLI 的版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中的 <log_file_name> 替换自己的服务器日志文件名。

```cli
#!/bin/bash

# Add the Azure CLI extension 
az extension add --name rdbms

# Create a resource group
az group create \
--name myresourcegroup  \
--location chinaeast2

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure
# Substitute the <server_admin_password> with your own value
az mysql server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location chinaeast2 \
--admin-user myadmin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2 \

# List the configuration options for review
az mysql server configuration list \
--resource-group myresourcegroup  \
--server mydemoserver

# Turn on statement level log
az mysql server configuration set \
--name log_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value all

# Set log_min_duration_statement time to 10 sec
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# List the available log files and direct to a text file
az mysql server-logs list \
--resource-group myresourcegroup \
--server mydemoserver > log_files_list.txt

# Download log file from Azure 
# Review log_files_list.txt to find the server log file name for the desired timeframe
# Substitute the <log_file_name> with your server log file name
# Creates the postgresql-<date>_000000.log file in the current command line path
az mysql server-logs download \
--name <log_file_name> \
--resource-group myresourcegroup \
--server mydemoserver
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```cli
#!/bin/bash
az group delete --name myresource
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/mysql/server#az-msql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server configuration list](/cli/mysql/server/configuration#az-mysql-server-configuration-list) | 列出服务器的配置值。 |
| [az mysql server configuration set](/cli/mysql/server/configuration#az-mysql-server-configuration-set) | 更新服务器的配置。 |
| [az mysql server-logs list](/cli/mysql/server-logs#az-mysql-server-logs-list) | 列出服务器的日志文件。 |
| [az mysql server-logs download](/cli/mysql/server-logs#az-mysql-server-logs-download) | 下载日志文件。 |
| [az group delete](/cli/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。
- 尝试其他脚本：[Azure Database for MySQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)