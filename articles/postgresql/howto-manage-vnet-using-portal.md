---
title: 使用 Azure 门户在 Azure Database for PostgreSQL（单一服务器）中创建并管理 VNet 服务终结点和规则
description: 使用 Azure 门户在 Azure Database for PostgreSQL（单一服务器）中创建并管理 VNet 服务终结点和规则
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 5/6/2019
ms.date: 05/20/2019
ms.openlocfilehash: 65c158d76a3d4d2e1b4282e76a50d62bb6857ce3
ms.sourcegitcommit: 11d81f0e4350a72d296e5664c2e5dc7e5f350926
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65732020"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for PostgreSQL（单一服务器）中创建并管理 VNet 服务终结点和 VNet 规则
虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到你的 Azure Database for PostgreSQL 服务器。 有关 Azure Database for PostgreSQL VNet 服务终结点（包括限制）的概述，请参阅 [Azure Database for PostgreSQL Server VNet 服务终结点](concepts-data-access-and-security-vnet.md)。 在 Azure Database for PostgreSQL 的所有支持区域中，VNet 服务终结点均可用。

> [!NOTE]
> 只有常规用途和内存优化服务器才支持 VNet 服务终结点。
> 在 VNet 对等互连的情况下，如果流量通过具有服务终结点的公共 VNet 网关，且需要流向对等机，请创建 ACL/VNet 规则，以便网关 VNet 中的 Azure 虚拟机能够访问 Azure Database for PostgreSQL 服务器。

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>在 Azure 门户中创建 VNet 规则和启用服务终结点

1. 在 PostgreSQL 服务器页上的“设置”标题下，单击“连接安全性”，打开 Azure Database for PostgreSQL 的“连接安全性”窗格。 

2. 确保将“允许访问 Azure 服务”控件设置为“关闭”。

> [!Important]
> 如果将此控件设置为“启用”，则 Azure PostgreSQL 数据库服务器接受来自任何子网的通信。 从安全角度来看，将此控件设置为“启用”可能会导致过度访问。 Azure 虚拟网络服务终结点功能与 Azure Database for PostgreSQL 的虚拟网络规则功能一起使用可以降低安全风险。

3. 接下来，单击“+ 添加现有虚拟网络”。 若无现有 VNet，可以单击“+ 新建虚拟网络”来创建一个。 请参阅[快速入门：使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)

   ![Azure 门户 - 单击“连接安全性”](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. 输入 VNet 规则名称，选择订阅、虚拟网络和子网名称，再单击“启用”。 这会使用 Microsoft.SQL 服务标记自动对子网启用 VNet 服务终结点。

   ![Azure 门户 - 配置 VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    该帐户必须拥有创建虚拟网络和服务终结点所需的必要权限。

    对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。
    
    若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。
    
    详细了解[内置角色](/active-directory/role-based-access-built-in-roles)以及将特定的权限分配到[自定义角色](/active-directory/role-based-access-control-custom-roles)。
    
    VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，资源应在相同的 Active Directory (AD) 租户下。

   > [!IMPORTANT]
   > 强烈建议在配置服务终结点前，先阅读本文介绍的服务终结点配置和注意事项。 **虚拟网络服务终结点：**[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用服务类型名称 Microsoft.Sql，可引用名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for PostgreSQL 和 MySQL 服务。 请务必要注意，对 VNet 服务终结点应用 Microsoft.Sql 服务标记时，它会为所有 Azure 数据库服务配置服务终结点流量，其中包括 Azure SQL 数据库、Azure Database for PostgreSQL 和子网上的 Azure Database for MySQL 服务器。 
   > 

5. 启用后，单击“确定”即可看到 VNet 服务终结点与 VNet 规则一起启用。

   ![VNet 服务终结点已启用，且 VNet 规则已创建](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>后续步骤
- 同样，可以将脚本编写为[使用 Azure CLI 为 Azure Database for PostgreSQL 启用 VNet 服务终结点和创建 VNET 规则](howto-manage-vnet-using-cli.md)。
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](./concepts-connection-libraries.md)
