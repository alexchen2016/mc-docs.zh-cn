---
title: 快速入门：在 Azure SQL 数据仓库中横向扩展计算资源 - Azure 门户 | Microsoft Docs
description: 使用 Azure 门户在 Azure SQL 数据仓库中缩放计算资源。 横向扩展计算为提高性能或缩放重新计算以节约成本。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
origin.date: 04/17/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 94ea91b18b640a998d64b65a9ff1b30bf68c40c5
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663797"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure SQL 数据仓库中缩放计算资源

使用 Azure 门户在 Azure SQL 数据仓库中缩放计算资源。 [横向扩展计算](sql-data-warehouse-manage-compute-overview.md)以提高性能或按比例缩减计算以节约成本。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://www.azure.cn/pricing/1rmb-trial/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.cn/)。

## <a name="before-you-begin"></a>准备阶段

可以缩放现有的数据仓库，或使用[创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的数据仓库。  本快速入门教程缩放 **mySampleDataWarehouse**。

## <a name="scale-compute"></a>缩放计算

在 SQL 数据仓库中，可以通过调整数据仓库单位来增加或减少计算资源。 [创建和 Connect - 门户](create-data-warehouse-portal.md)创建 **mySampleDataWarehouse** 并初始化 400 DWU。 以下步骤调整为 DWU **mySampleDataWarehouse**。

更改数据仓库单位：

1. 在 Azure 门户的左侧页面中，单击“SQL 数据仓库”。
2. 从“SQL 数据仓库”页中选择“mySampleDataWarehouse”。 此操作打开数据仓库。
3. 单击“缩放”。

    ![单击“缩放”](media/quickstart-scale-compute-portal/click-scale.png)

2. 在“缩放”窗格中，向左或向右移动滑块，以更改 DWU 设置。

    ![移动滑块](media/quickstart-scale-compute-portal/scale-dwu.png)

3. 单击“保存”。 此时会显示确认消息。 单击“是”以确认或“否”以取消。

    ![点击“保存”(Save)](media/quickstart-scale-compute-portal/confirm-change.png)

## <a name="next-steps"></a>后续步骤
现在已暂停并恢复了数据仓库的计算。 若要了解有关 Azure SQL 数据仓库的详细信息，请继续有关加载数据的教程。

> [!div class="nextstepaction"]
>[将数据加载到 SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)
<!-- Update_Description: new articles on quickstart scale database on portal -->
<!--ms.date: 03/12/2018-->