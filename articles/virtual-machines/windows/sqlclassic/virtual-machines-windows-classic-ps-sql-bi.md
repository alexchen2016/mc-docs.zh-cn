---
title: SQL Server Business Intelligence | Azure
description: 本主题使用通过经典部署模型创建的资源，并介绍了为 Azure 虚拟机 (VM) 上运行的 SQL Server 提供的 Business Intelligence (BI) 功能。
services: virtual-machines-windows
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
origin.date: 05/30/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 6c222ef7d90bb63f038944e11b7bbef2fa6d3935
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003731"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Azure 虚拟机中的 SQL Server Business Intelligence
> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。

Azure 虚拟机库包括含有 SQL Server 安装的映像。 库映像中支持的 SQL Server 版本是可以安装到本地计算机和虚拟机的相同安装文件。 本主题总结了在映像上安装的 SQL Server Business Intelligence (BI) 功能和预配虚拟机后所需的配置步骤。 本主题还介绍了 BI 功能的支持部署拓扑和最佳实践。

## <a name="license-considerations"></a>许可证注意事项
可通过两种方式授权 Azure 虚拟机中的 SQL Server：

1. 属于软件保障的许可证移动性权益。 有关详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://www.azure.cn/pricing/license-mobility/)。
2. 已安装 SQL Server 的 Azure 虚拟机按小时付费。 请参阅[虚拟机定价](https://www.azure.cn/pricing/details/virtual-machines/)中的“SQL Server”部分。
<!-- Not Available on [Virtual Machines Licensing FAQ](https://www.azure.cn/pricing/licensing-faq/)-->

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>在 Azure 虚拟机库中提供的 SQL Server 映像
Azure 虚拟机库包括多个内含 Microsoft SQL Server 的映像。 虚拟机映像上安装的软件根据操作系统版本和 SQL Server 版本而有所不同。 Azure 虚拟机库中提供的映像列表频繁更改。

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Azure VM 库中的 SQL 映像](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 以下 PowerShell 脚本返回 ImageName 中包含“SQL-Server”的 Azure 映像列表：

    # assumes you have already uploaded a management certificate to your Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

有关 SQL Server 支持的版本和功能的详细信息，请参阅以下各部分：

* [SQL Server 版本](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [SQL Server 2016 版本支持的功能](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>SQL Server 虚拟机库映像上安装的 BI 功能
下表总结了在适用于 SQL Server 的常见 Azure 虚拟机库映像上安装的 Business Intelligence 功能:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| SQL Server BI 功能 | 在库映像上安装的 | 注释 |
| --- | --- | --- |
| **Reporting Services 本机模式** |是 |已安装但需要配置，包括报表管理器 URL。 请参阅 [配置 Reporting Services](#configure-reporting-services)部分。 |
| **Reporting Services SharePoint 模式** |否 |Azure 虚拟机库映像不包括 SharePoint 或 SharePoint 安装文件。 <sup>1</sup> |
| **Analysis Services 多维和数据挖掘 (OLAP)** |是 |作为默认 Analysis Services 实例安装和配置 |
| **Analysis Services 表格** |否 |在 SQL Server 2012、2014 和 2016 映像中受支持，但未默认安装。 安装 Analysis Services 的另一个实例。 请参阅本主题中的“安装其他 SQL Server 服务和功能”部分。 |
| **用于 SharePoint 的 Analysis Services Power Pivot** |否 |Azure 虚拟机库映像不包括 SharePoint 或 SharePoint 安装文件。 <sup>1</sup> |

<sup>1</sup> 有关 SharePoint 和 Azure 虚拟机的其他信息，请参阅[适用于 SharePoint 2013 的 Azure 体系结构](https://technet.microsoft.com/library/dn635309.aspx)和 [Azure 虚拟机上的 SharePoint 部署](https://www.microsoft.com/download/details.aspx?id=34598)。

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 运行以下 PowerShell 命令可获取服务名称中包含“SQL”的已安装服务列表。

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>一般建议和最佳实践
* 在使用 SQL Server Enterprise Edition 时，虚拟机的最小建议大小是 **A3** 。 对于 Analysis Services 和 Reporting Services 的 SQL Server BI 部署，其建议虚拟机大小为 **A4** 。

    有关当前 VM 大小的信息，请参阅 [Azure 的虚拟机大小](../sizes.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)。
* 磁盘管理的最佳做法是将数据、日志和备份文件存储在驱动器上，而不是 C: 和 D:上。 例如，创建数据磁盘 E: 和 F:。

    * 默认驱动器 **C**: 的驱动器缓存策略未针对处理数据进行优化。
    * **D**: 驱动器是主要用于页面文件的临时驱动器。 **D**: 驱动器不会持久保留且不保存在 blob 存储中。 诸如更改虚拟机大小之类的管理任务会重置 **D**: 驱动器。 建议不要将 D: 驱动器用于存储数据库文件（包括 tempdb）。

    有关创建和附加磁盘的详细信息，请参阅[如何将数据磁盘附加到虚拟机](../classic/attach-disk-classic.md?toc=%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* 停止或卸载计划不使用的服务。 例如，如果虚拟机仅用于 Reporting Services，停止或卸载 Analysis Services 和 SQL Server Integration Services。 下图是默认情况下启动的服务的示例。

    ![SQL Server 服务](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    > [!NOTE]
    > 支持的 BI 方案中需要 SQL Server 数据库引擎。 在单服务器 VM 拓扑中，数据库引擎需要在同一个 VM 上运行。

    有关详细信息，请参阅以下部分：[卸载 Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) 和[卸载 Analysis Services 实例](https://msdn.microsoft.com/library/ms143687.aspx)。
* 检查 **Windows 更新** 以获取新的“重要更新”。 Azure 虚拟机映像会经常刷新；但在最近刷新 VM 映像后， **Windows 更新** 可能还会提供重要更新。

## <a name="example-deployment-topologies"></a>部署拓扑示例
以下是使用 Azure 虚拟机的部署示例。 这些图中的拓扑仅是一些可用于 SQL Server BI 功能和 Azure 虚拟机的可能拓扑。

### <a name="single-virtual-machine"></a>单个虚拟机
Analysis Services、Reporting Services、SQL Server 数据库引擎和数据源在单个虚拟机上。

![使用 1 个虚拟机的 bi iass 方案](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>两个虚拟机
* Analysis Services、Reporting Services 和 SQL Server 数据库引擎在单个虚拟机上。 这种部署包括报表服务器数据库。
* 数据源在第二个 VM 上。 第二个 VM 包括 SQL Server 数据库引擎作为数据源。

![使用 2 个虚拟机的 bi iass 方案](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure---data-on-azure-sql-database"></a>Azure SQL 数据库上的混合 Azure 数据
* Analysis Services、Reporting Services 和 SQL Server 数据库引擎在单个虚拟机上。 这种部署包括报表服务器数据库。
* 数据源是 Azure SQL 数据库。

![bi iaas 方案 VM 和 AzureSQL 作为数据源](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid--data-on-premises"></a>本地混合数据
* 在此部署示例中，Analysis Services、Reporting Services 和 SQL Server 数据库引擎在单个虚拟机上运行。 该虚拟机托管报表服务器数据库。 该虚拟机通过 Azure 虚拟网络或其他 VPN 隧道解决方案加入到本地域。
* 数据源是在本地。

![bi iaas 方案 VM 和本地数据源](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Reporting Services 本机模式配置
SQL Server 的虚拟机库映像包括安装的 Reporting Services 本机模式，但未配置报表服务器。 本部分中的步骤配置 Reporting Services 报表服务器。 有关配置 Reporting Services 本机模式的更多详细信息，请参阅[安装 Reporting Services 本机模式报表服务器 (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx)。

> [!NOTE]
> 有关使用 Windows PowerShell 脚本配置报表服务器的类似内容，请参阅[使用 PowerShell 创建运行本机模式报表服务器的 Azure VM](../classic/ps-sql-report.md)。

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>连接到虚拟机并启动 Reporting Services 配置管理器
连接到 Azure 虚拟机有两个常见工作流：

* 若要连接，请单击虚拟机的名称，然后单击“连接”。 远程桌面连接打开并自动填充计算机名称。

    ![连接到 Azure 虚拟机](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* 通过 Windows 远程桌面连接到虚拟机。 在远程桌面的用户界面中：

    1. 键入 **云服务名称** 作为计算机名称。
    2. 键入冒号 (:) 和为 TCP 远程桌面终结点配置的公共端口号。

            Myservice.chinacloudapp.cn:63133

        有关详细信息，请参阅[什么是云服务？](/cloud-services/cloud-services-choose-me)。

**启动 Reporting Services 配置管理器**

在 **Windows Server 2012/2016**中：

1. 在“开始”屏幕中，键入 Reporting Services，查看应用列表。
2. 右键单击“Reporting Services 配置管理器”，然后单击“以管理员身份运行”。

在 **Windows Server 2008 R2**中：

1. 单击“开始”，然后单击“所有程序”。
2. 单击“Microsoft SQL Server 2016” 。
3. 单击“Microsoft SQL Server 2016” 。
4. 右键单击“Reporting Services 配置管理器”，然后单击“以管理员身份运行”。

或：

1. 单击“启动”。
2. 在“搜索程序和文件”对话框中，键入 reporting services。 **reporting services** 。
3. 右键单击“Reporting Services 配置管理器”，然后单击“以管理员身份运行”。

    ![搜索 ssrs 配置管理器](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>配置 Reporting Services
**服务帐户和 Web 服务 URL：**

1. 验证“服务器名称”是否是本地服务器名称，然后单击“连接”。
2. 注意空白“报表服务器数据库名称”。 配置完成时创建该数据库。
3. 确认“报表服务器状态”为“已启动”。 **SQL Server Reporting Services** Windows 服务。
4. 单击“服务帐户”并根据需要更改帐户。 如果在未加入域的环境中使用虚拟机，使用内置 ReportServer 帐户就已足够。 有关服务帐户的详细信息，请参阅[服务帐户](https://msdn.microsoft.com/library/ms189964.aspx)。
5. 在左侧窗格中，单击“Web 服务 URL”。
6. 单击“应用”，配置默认值。
7. 请注意 **报表服务器 Web 服务 URL**。 请注意，默认的 TCP 端口为 80 并且是 URL 的一部分。 在后续步骤中，为该端口创建 Azure 虚拟机终结点。
8. 在“结果”窗格中，验证是否已成功完成操作。

**数据库：**

1. 在左侧窗格中，单击“数据库”。
2. 单击“更改数据库”。
3. 验证是否已选中“创建新的报表服务器数据库”，然后单击“下一步”。
4. 验证“服务器名称”并单击“测试连接”。
5. 如果结果为“连接测试成功”，单击“确定”，然后单击“下一步”。
6. 请注意数据库名称是 ReportServer，“报表服务器模式”是“本机”，然后单击“下一步”。
7. 在“凭据”页上单击“下一步”。
8. 在“摘要”页上单击“下一步”。
9. 在“进度和完成”页上单击“下一步”。

**2012 和 2014 版的 Web 门户 URL 或报表管理器 URL：**

1. 在左侧窗格中，单击适用于 2014 和 2012 的“Web 门户 URL”或“报表服务器 URL”。
2. 单击“应用” 。
3. 在“结果”窗格中，验证是否已成功完成操作。
4. 单击“退出”。

有关报表服务器权限的信息，请参阅[授予本机模式报表服务器权限](https://msdn.microsoft.com/library/ms156014.aspx)。

### <a name="browse-to-the-local-report-manager"></a>浏览到本地报表管理器
若要验证配置，浏览到 VM 上的报表管理器。

1. 在 VM 中，使用管理员权限启动 Internet Explorer。
2. 在 VM 上浏览到 http:\//localhost/reports。

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>连接到 2012 和 2014 版的远程 Web 门户或报表管理器
如果想要从远程计算机连接到虚拟机上的 2012 和 2014 版 Web 门户或报表管理器，请创建新的虚拟机 TCP 终结点。 默认情况下，报表服务器侦听“端口 80”上的 HTTP 请求。 如果将报表服务器 URL 配置为使用其他端口，必须在下面的说明中指定该端口号。

1. 为虚拟机创建终结点 TCP 端口 80。 有关详细信息，请参阅本文档中的 [虚拟机终结点以及防火墙端口](#virtual-machine-endpoints-and-firewall-ports) 部分。
2. 在虚拟机的防火墙中打开端口 80。
3. 使用 Azure 虚拟机 **DNS 名称** 作为 URL 中的服务器名称浏览到 Web 门户。 例如：

    **报表服务器**： http://uebi.chinacloudapp.cn/reportserver  **Web 门户**： http://uebi.chinacloudapp.cn/reports

    [针对报表服务器访问配置防火墙](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>创建报表并将其发布到 Azure 虚拟机
下表汇总一些选项，可用于将现有报表从本地计算机发布到 Azure 虚拟机上托管的报表服务器：

* **报表生成器**：虚拟机包括适用于 SQL 2014 和 2012 的 Microsoft SQL Server 报表生成器的一键式版本。 若要首次在虚拟机上通过 SQL 2016 启动报表生成器：

    1. 使用管理权限启动浏览器。
    2. 在虚拟机上浏览到 Web 门户，并选择右上角的“下载”  图标。
    3. 选择“报表生成器” 。

     有关详细信息，请参阅[启动报表生成器](https://msdn.microsoft.com/library/ms159221.aspx)。
* **SQL Server Data Tools**：VM：SQL Server Data Tools 安装在虚拟机上，可用于在该虚拟机上创建报表服务器项目和报表。 SQL Server Data Tools 可以将报表发布到虚拟机上的报表服务器。
* **SQL Server Data Tools：远程**：在本地计算机上，在 SQL Server Data Tools 中创建一个包含 Reporting Services 报表的 Reporting Services 项目。 将项目配置为连接到 Web 服务 URL。

    ![SSRS 项目的 ssdt 项目属性](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* 创建一个包含报表的 .VHD 硬盘驱动器，然后上传并附加该驱动器。

    1. 在本地计算机上创建一个包含报表的 .VHD 硬盘驱动器。
    2. 创建并安装管理证书。
    3. 使用 Add-AzureVHD cmdlet 将 VHD 文件上传到 Azure [创建 Windows Server VHD 并将其上传到 Azure](../classic/createupload-vhd.md?toc=%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
    4. 将磁盘附加到虚拟机。

## <a name="install-other-sql-server-services-and-features"></a>安装其他 SQL Server 服务和功能
若要安装其他 SQL Server 服务（如表格模式下的 Analysis Services），运行 SQL Server 安装向导。 安装程序文件位于虚拟机的本地磁盘上。

1. 单击“开始”，然后单击“所有程序”。
2. 单击“Microsoft SQL Server 2016”、“Microsoft SQL Server 2014”或“Microsoft SQL Server 2012”，然后单击“配置工具”。
3. 单击“SQL Server 安装中心” 。

或运行 C:\SQLServer_13.0_full\setup.exe、C:\SQLServer_12.0_full\setup.exe 或 C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> 首次运行 SQL Server 安装程序时可能会下载更多安装文件并需要重新启动虚拟机和重新启动 SQL Server 安装程序。
> 
> 如果需要反复自定义从 Azure 虚拟机中选择的映像，请考虑创建自己的 SQL Server 映像。 Analysis Services SysPrep 功能在 SQL Server 2012 SP1 CU2 中已启用。 有关详细信息，请参阅 [Considerations for Installing SQL Server Using SysPrep](https://msdn.microsoft.com/library/ee210754.aspx)（使用 SysPrep 安装 SQL Server 的注意事项）和 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)（针对服务器角色的 Sysprep 支持）。
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>若要安装 Analysis Services 表格模式
本部分中的步骤 **汇总** 了 Analysis Services 表格模式的安装。 有关详细信息，请参阅以下部分：

* [在表格模式下安装 Analysis Services](https://msdn.microsoft.com/library/hh231722.aspx)
* [表格建模（Adventure Works 教程）](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**若要安装 Analysis Services 表格模式，请执行以下操作：**

1. 在 SQL Server 安装向导中，单击左侧窗格中的“安装”，然后单击“新的 SQL Server 独立安装或向现有安装添加功能”。

    * 如果看到“浏览文件夹”，请浏览到 c:\SQLServer_13.0_full、c:\SQLServer_12.0_full 或 c:\SQLServer_11.0_full，然后单击“确定”。
2. 在产品更新页面上单击“下一步”。
3. 在“安装类型”页上，选择“对 SQL Server 执行全新安装”，然后单击“下一步”。
4. 在“安装角色”页上，单击“SQL Server 功能安装”。
5. 在“功能选择”页上，单击“Analysis Services”。
6. 在“实例配置”页上，在“命名实例”和“实例 ID”文本框中键入一个描述性名称，如“表格”。
7. 在“Analysis Services 配置”页上，选择“表格模式”。 将当前用户添加到管理权限列表。
8. 完成并关闭 SQL Server 安装向导。

## <a name="analysis-services-configuration"></a>Analysis Services 配置
### <a name="remote-access-to-analysis-services-server"></a>对 Analysis Services 服务器的远程访问
Analysis Services 服务器仅支持 windows 身份验证。 若要从客户端应用程序（如 SQL Server Management Studio 或 SQL Server Data Tools）远程访问 Analysis Services，虚拟机需要使用 Azure 虚拟网络加入到本地域。 有关详细信息，请参阅 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。

Analysis Services 的默认实例侦听 TCP 端口 2383。 在虚拟机防火墙中打开该端口。 Analysis Services 的群集命名实例也侦听端口 **2383**。

对于 Analysis Services 的 **命名实例** ，需要 SQL Server Browser 服务来管理端口访问。 SQL Server Browser 默认配置是端口 **2382**。

在虚拟机防火墙中，打开端口 **2382** 并创建一个静态 Analysis Services 命名实例端口。

1. 若要验证在 VM 上已使用的端口和哪个进程正在使用该端口，请使用管理权限运行以下命令：

        netstat /ao
2. 使用 SQL Server Management Studio 通过更新表格 AS 实例常规属性中的“端口”值来创建一个静态的 Analysis Services 命名实例端口。 有关详细信息，请参阅 [Configure the Windows Firewall to Allow Analysis Services Access](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed)（将 Windows 防火墙配置为允许 Analysis Services 访问）中的“对默认或命名实例使用固定端口”。
3. 重启 Analysis Services 服务的表格实例。

有关详细信息，请参阅本文档中的**虚拟机终结点以及防火墙端口**部分。

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>虚拟机终结点以及防火墙端口
本部分总结了要创建的 Azure 虚拟机终结点以及要在虚拟机防火墙中打开的端口。 下表总结了要为其创建终结点的 **TCP** 端口和要在虚拟机防火墙中打开的端口。

* 如果使用的是单个 VM 并且下列两项为 true，不需要创建 VM 终结点并且不需要在 VM 上的防火墙中打开端口。

    * 未远程连接到 VM 上的 SQL Server 功能。 与 VM 建立远程桌面连接和从本地访问 VM 上的 SQL Server 功能，不被视为与 SQL Server 功能远程连接。
    * 不通过 Azure 虚拟网络或其他 VPN 隧道解决方案将 VM 加入到本地域。
* 如果虚拟机未加入到域，但希望远程连接到 VM 上的 SQL Server 功能：

    * 在 VM 防火墙中打开端口。
    * 为前述端口 (*) 创建虚拟机终结点。
* 如果虚拟机使用 Azure 虚拟网络等 VPN 隧道加入域，则不需要终结点。 但是要在 VM 防火墙中打开端口。

    | 端口 | 类型 | 说明 |
    | --- | --- | --- |
    | **80** |TCP |报表服务器远程访问 (*)。 |
    | **1433** |TCP |SQL Server Management Studio (*)。 |
    | **1434** |UDP |SQL Server Browser。 当 VM 加入到域时需要。 |
    | **2382** |TCP |SQL Server Browser。 |
    | **2383** |TCP |SQL Server Analysis Services 默认实例和群集命名实例。 |
    | **用户定义** |TCP |为用户选择的端口号创建一个静态 Analysis Services 命名实例端口，并在防火墙中解锁该端口号。 |

有关创建终结点的详细信息，请参阅以下资源：

* 创建终结点：[如何设置虚拟机的终结点](../classic/setup-endpoints.md?toc=%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* SQL Server：请参阅[在 Azure 上预配 SQL Server 虚拟机](../sql/virtual-machines-windows-portal-sql-server-provision.md)的“完成配置步骤以使用 SQL Server Management Studio 连接到虚拟机”部分。

下图说明了要允许远程访问 VM 上的功能和组件，需要在 VM 防火墙上打开的端口。

![要为 Azure VM 中的 bi 应用程序打开的端口](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>资源
* 查看在 Azure 虚拟机环境中使用的 Microsoft 服务器软件的支持策略。 以下主题概述了对 BitLocker、故障转移群集和网络负载均衡等功能的支持。 [Microsoft 服务器软件对 Azure 虚拟机的支持](http://support.microsoft.com/kb/2721672)。
* [Azure 虚拟机上的 SQL Server 概述](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [虚拟机](/virtual-machines/)
* [在 Azure 上预配 SQL Server 虚拟机](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [如何将数据磁盘附加到虚拟机](../classic/attach-disk-classic.md?toc=%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [将数据库迁移到 Azure VM 上的 SQL Server](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [确定 Analysis Services 实例的服务器模式](https://msdn.microsoft.com/library/gg471594.aspx)
* [多维建模（Adventure Works 教程）](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure 文档中心](https://www.azure.cn/documentation/)
* [在混合环境中使用 Power BI](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [通过 Microsoft SQL Server Connect 提交反馈和联系人信息](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>社区内容
* [使用 PowerShell 管理 Azure SQL 数据库](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

<!-- Update_Description: wording update, update link -->
