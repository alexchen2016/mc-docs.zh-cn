---
title: 从 Azure 数据资源管理器中删除数据
description: 本文介绍了 Azure 数据资源管理器中的批量删除方案，包括清除和基于保留期的删除。
author: orspod
ms.author: v-biyu
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 05/01/2019
ms.openlocfilehash: 6e4b8b701fc614eea7c375ddee20f8a1c002fb28
ms.sourcegitcommit: bf3df5d77e5fa66825fe22ca8937930bf45fd201
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59686592"
---
# <a name="delete-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器中删除数据

Azure 数据资源管理器支持多种批量删除方式，我们在本文中介绍了这些方式。 它不支持实时的每记录删除，因为它针对快速读取访问进行了优化。

* 如果不再需要一个或多个表，请使用 drop table 或 drop tables 命令删除它们。

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* 如果不再需要旧数据，请通过在数据库或表级别更改保留期来将其删除。

    假设某个数据库或表的保留期设置为 90 天。 由于业务需求发生了变化，因此现在仅需要 60 天的数据。 在此情况下，可以通过下列方式之一删除较旧的数据。

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    有关详细信息，请参阅[保留策略](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy)。

如果在解决数据删除问题时需要帮助，请在 [Azure 门户](https://portal.azure.cn/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中打开一个支持请求。
