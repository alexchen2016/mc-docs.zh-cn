---
title: 配置 P2S - Azure SQL 数据库托管实例 | Microsoft Docs
description: 使用本地客户端计算机的点到站点连接，通过 SQL Server Management Studio 连接到 Azure SQL 数据库托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab, bonova, jovanpop
manager: digimobile
origin.date: 03/13/2019
ms.date: 04/08/2019
ms.openlocfilehash: 1761b1930fad777eba13ee7f2dd3cb1b39180be1
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003481"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>快速入门：配置从本地到 Azure SQL 数据库托管实例的点到站点连接

本快速入门演示如何使用本地客户端计算机的点到站点连接，通过 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) 连接到 Azure SQL 数据库托管实例。 有关点到站点连接的信息，请参阅[关于点到站点 VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>先决条件

本快速入门：

- 从[创建托管实例](sql-database-managed-instance-get-started.md)中创建的资源着手。
- 本地客户端计算机需要 PowerShell 5.1 和 AZ PowerShell 1.4.0 或更高版本。 必要时，请根据说明来[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)。
- 需要本地客户端计算机上的最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>将 VPN 网关附加到托管实例虚拟网络

1. 在本地客户端计算机上打开 PowerShell。

2. 复制此 PowerShell 脚本。 此脚本将 VPN 网关附加到在[创建托管实例](sql-database-managed-instance-get-started.md)快速入门中创建的托管实例虚拟网络。 此脚本执行以下任务：

   - 在客户端计算机上创建并安装证书
   - 计算未来的 VPN 网关子网 IP 范围
   - 创建 GatewaySubnet
   - 部署可将 VPN 网关附加到 VPN 子网的 Azure 资源管理器模板

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > 若要使用 Azure PowerShell 资源管理器模块而不是 Az 模块，请使用 `attachVPNGateway.ps1` cmdlet 而不是 `attachVPNGatewayAz.ps1` cmdlet。

3. 在 PowerShell 窗口中粘贴脚本，并提供所需参数。 `<subscriptionId>`、`<resourceGroup>`、`<virtualNetworkName>` 的值应匹配用于[创建托管实例](sql-database-managed-instance-get-started.md)快速入门的值。 `<certificateNamePrefix>` 的值可以是所选字符串。

4. 执行 PowerShell 脚本。

> [!IMPORTANT]
> 在 PowerShell 脚本完成前，请勿继续操作。

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>创建连接到托管实例的 VPN 连接

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 打开在其中创建了虚拟网关的资源组，然后打开虚拟网关资源。
3. 选择“点到站点配置”，然后选择“下载 VPN 客户端”。

    ![下载 VPN 客户端](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. 在本地客户端计算机上，从 zip 文件中提取文件，然后打开包含已提取文件的文件夹。
5. 打开 **WindowsAmd64** 文件夹，然后打开 **VpnClientSetupAmd64.exe** 文件。
6. 如果收到“Windows 已保护你的电脑”消息，请单击“更多信息”，然后单击“仍然运行”。

    ![安装 VPN 客户端](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. 在“用户帐户控制”对话框中单击“是”，继续下一步。
8. 在引用虚拟网络的对话框中选择“是”，为虚拟网络安装 VPN 客户端。

## <a name="connect-to-the-vpn-connection"></a>连接到 VPN 连接

1. 在本地客户端计算机上的“网络和 Internet”中转到“VPN”，选择“托管实例”虚拟网络，以便建立到此 VNet 的连接。 在下图中，VNet 名为 **MyNewVNet**。

    ![VPN 连接](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. 选择“连接” 。
3. 在对话框中，选择“连接”。

    ![VPN 连接](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. 当系统提示你连接管理器需要提升的特权才能更新路由表时，请选择“继续”。
5. 在“用户帐户控制”对话框中选择“是”，继续下一步。

   已建立连接到托管实例 VNet 的 VPN 连接。

    ![VPN 连接](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>使用 SSMS 连接到托管实例

1. 在本地客户端计算机上，打开 SQL Server Management Studio (SSMS)。
2. 在“连接到服务器”对话框的“服务器名称”框中输入托管实例的完全限定**主机名**。
3. 选择“SQL Server 身份验证”，提供用户名和密码，然后选择“连接”。

    ![ssms 连接](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

连接后，可以在“数据库”节点中查看系统和用户数据库。 还可以在“安全性”、“服务器对象”、“复制”、“管理”、“SQL Server 代理”和“XEvent 探查器”节点中查看各种对象。

## <a name="next-steps"></a>后续步骤

- 如需介绍如何从 Azure 虚拟机进行连接的快速入门，请参阅[配置点到站点连接](sql-database-managed-instance-configure-p2s.md)。
- 如需应用程序的连接选项的概述，请参阅[将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md)。
- 若要将现有 SQL Server 数据库从本地还原到托管实例，可以使用 [T-SQL RESTORE 命令](sql-database-managed-instance-get-started-restore.md)从数据库备份文件还原。
