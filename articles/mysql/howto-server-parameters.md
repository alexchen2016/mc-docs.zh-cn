---
title: 如何在 Azure Database for MySQL 中配置服务器参数
description: 本文介绍如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置 MySQL 服务器参数。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 12/31/2018
ms.openlocfilehash: 0b850c42d4f02f020e3e74a4d2cdd4c6e1956144
ms.sourcegitcommit: 5fc46672ae90b6598130069f10efeeb634e9a5af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67236596"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置服务器参数

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。

## <a name="navigate-to-server-parameters-on-azure-portal"></a>在 Azure 门户中导航到“服务器参数”

1. 登录到 Azure 门户，然后定位到适用于 MySQL 服务器的 Azure 数据库。
2. 在“设置”  部分下，单击“服务器参数”  ，打开 Azure Database for MySQL 服务器的“服务器参数”页。
![Azure 门户中的服务器参数页](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 定位需要调整的任何设置。 查看“说明”列  ，了解用途和允许的值。
![枚举下拉按钮](./media/howto-server-parameters/3-toggle_parameter.png)
4. 单击“保存”  ，保存更改。
![保存或放弃更改](./media/howto-server-parameters/4-save_parameters.png)
5. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。 
![全部重置为默认设置](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可配置的服务器参数列表

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡，以根据应用程序要求获取定义并配置服务器参数。

## <a name="non-configurable-server-parameters"></a>不可配置的服务器参数

InnoDB 缓冲池和最大连接数不可配置，因[定价层](concepts-service-tiers.md)而定。

|**定价层**| **计算代**|**vCore(s)**|InnoDB 缓冲池 (MB) | 最大连接数 |
|---|---|---|---|--|
|基本| 第 4 代| 1| 960| 50|
|基本| 第 4 代| 2| 2560| 100|
|基本| 第 5 代| 1| 960| 50|
|基本| 第 5 代| 2| 2560| 100|
|常规用途| 第 4 代| 2| 3584| 300|
|常规用途| 第 4 代| 4| 7680| 625|
|常规用途| 第 4 代| 8| 15360| 1250|
|常规用途| 第 4 代| 16| 31232| 2500|
|常规用途| 第 4 代| 32| 62976| 5000|
|常规用途| 第 5 代| 2| 3584| 300|
|常规用途| 第 5 代| 4| 7680| 625|
|常规用途| 第 5 代| 8| 15360| 1250|
|常规用途| 第 5 代| 16| 31232| 2500|
|常规用途| 第 5 代| 32| 62976| 5000|
|常规用途| 第 5 代| 64| 125952| 10000|
|内存优化| 第 5 代| 2| 7168| 600|
|内存优化| 第 5 代| 4| 15360| 1250|
|内存优化| 第 5 代| 8| 30720| 2500|
|内存优化| 第 5 代| 16| 62464| 5000|
|内存优化| 第 5 代| 32| 125952| 10000|

以下附加服务器参数不可在系统中配置：

|**参数**|**固定值**|
| :------------------------ | :-------- |
|基本层中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

在版本 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 中，上表中未列出的其他服务器参数将设置为其 MySQL 现成默认值。

## <a name="next-steps"></a>后续步骤

- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)。
