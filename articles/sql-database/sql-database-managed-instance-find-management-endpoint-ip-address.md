---
title: 发现 Azure SQL 数据库托管实例管理终结点 | Microsoft Docs
description: 了解如何获取 Azure SQL 数据库托管实例管理终结点公用 IP 地址并验证其内置防火墙保护
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, carlrab
manager: digimobile
origin.date: 12/04/2018
ms.date: 05/20/2019
ms.openlocfilehash: eb8968396e33bda45a7bb933d22a03bc9a37c226
ms.sourcegitcommit: f0f5cd71f92aa85411cdd7426aaeb7a4264b3382
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2019
ms.locfileid: "65629251"
---
# <a name="determine-the-management-endpoint-ip-address"></a>确定管理终结点 IP 地址

Azure SQL 数据库托管实例虚拟群集包含一个管理终结点，可供 Azure 用来执行管理操作。 管理终结点在网络级别受内置防火墙保护，在应用程序级别受相互证书验证保护。 你可以确定管理终结点的 IP 地址，但无法访问此终结点。

## <a name="determine-ip-address"></a>确定 IP 地址

让我们假设托管实例主机是 `mi-demo.xxxxxx.database.chinacloudapi.cn`。 使用主机名运行 `nslookup`。

![解析内部主机名](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

现在，针对突出显示的名称运行另一个 `nslookup` 命令，删除 `.vnet.` 段。 执行此命令后，你将得到公用 IP 地址。

![解析公用 IP 地址](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>后续步骤

有关托管实例和连接的详细信息，请参阅 [Azure SQL 数据库托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
