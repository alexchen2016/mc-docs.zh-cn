---
title: 在 Azure Database for PostgreSQL - 单一服务器中创建和管理防火墙规则
description: 使用 Azure 门户在 Azure Database for PostgreSQL - 单一服务器中创建和管理防火墙规则
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 5/6/2019
ms.date: 05/20/2019
ms.openlocfilehash: a3c9e003d714a51b6f5235828a811b4be8ebf867
ms.sourcegitcommit: 11d81f0e4350a72d296e5664c2e5dc7e5f350926
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65732022"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for PostgreSQL - 单一服务器中创建和管理防火墙规则
可以使用服务器级防火墙规则，管理从指定的 IP 地址或某个范围的 IP 地址对 Azure Database for PostgreSQL 服务器的访问。

也可使用虚拟网络 (VNet) 规则来保护对服务器进行的访问。 详细了解如何[使用 Azure 门户创建和管理虚拟网络服务终结点和规则](howto-manage-vnet-using-portal.md)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 一个服务器[创建 Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级别的防火墙规则
1. 在 PostgreSQL 服务器页上的“设置”标题下，单击“连接安全性”，打开 Azure Database for PostgreSQL 的“连接安全性”页。

   ![Azure 门户 - 单击连接安全性](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. 在工具栏上单击“添加我的 IP”。 该操作会自动创建一条防火墙规则，其中包含计算机的公共 IP 地址（由 Azure 系统标识）。

   ![Azure 门户 - 单击“添加我的 IP”](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 验证 IP 地址，并保存配置。 在某些情况下，Azure 门户识别出的 IP 地址与访问 Internet 和 Azure 服务器时所使用的 IP 地址不同。 因此，可能需要更改起始 IP 和结束 IP，以使规则正常工作。
   使用搜索引擎或其他联机工具来查看自己的 IP 地址。 例如，搜索“我的 IP 是多少”。

   ![在必应中搜索“我的 IP 是多少”](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 添加其他地址范围。 在 Azure Database for PostgreSQL 防火墙规则中，可以指定单个 IP 地址，也可以指定某个范围的地址。 如果希望将规则限制为单个 IP 地址，请在起始 IP 和结束 IP 字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序可以访问 PostgreSQL 服务器上他们拥有有效凭据的任何数据库。

   ![Azure 门户 - 防火墙规则](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. 在工具栏上单击“保存”以保存此服务器级防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

   ![Azure 门户 - 单击“保存”](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>从 Azure 连接
若要允许来自 Azure 的应用程序连接到 Azure Database for PostgreSQL 服务器，必须启用 Azure 连接。 例如，如果要承载“Azure Web 应用”应用程序，或者要承载在 Azure VM 中运行的应用程序。 资源无需在同一虚拟网络 (VNet) 或资源组中，即可使用防火墙规则启用这些连接。 在应用程序尝试从 Azure 连接到你的数据库服务器时，防火墙将验证是否允许 Azure 连接。 有几种方法可启用这些类型的连接。 如果防火墙设置的开始地址和结束地址都等于 0.0.0.0，则表示允许这些连接。 或者，可以在门户中从“连接安全性”窗格将“允许访问 Azure 服务”选项设为“启用”并点击“保存”。 如果不允许该连接尝试，则该请求将不会访问 Azure Database for PostgreSQL 服务器。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复这些步骤来管理防火墙规则。
* 若要添加当前计算机，请单击“添加我的 IP”的 + 按钮。 单击“保存”  以保存更改。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击“保存”  以保存更改。
* 若要修改现有规则，单击规则中的任意字段并修改。 单击“保存”  以保存更改。
* 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则。 单击“保存”  以保存更改。

## <a name="next-steps"></a>后续步骤
- 同样，请参阅[使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-cli.md)。
- 通过[使用 Azure 门户创建和管理虚拟网络服务终结点和规则](howto-manage-vnet-using-portal.md)，进一步保护对服务器的访问。
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)。
