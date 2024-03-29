---
title: 在 Azure 中将作业提交到 HPC Pack 群集 | Azure
description: 了解如何设置本地计算机，以将作业提交到 Azure 中的 HPC Pack 群集
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
origin.date: 05/14/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 91bc322c24169fd2a9e1f207322a818cbcdfb54c
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626296"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>将 HPC 作业从本地计算机提交到部署在 Azure 中的 HPC Pack 群集
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

配置本地客户端计算机，将作业提交到 Azure 中的 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 群集。 本文介绍如何使用客户端工具设置本地计算机，通过 HTTPS 将作业提交到 Azure 中的群集。 借此，多个群集用户可将作业提交到基于云的 HPC Pack 群集中，而无需直接连接到头节点 VM 或访问 Azure 订阅。

![向 Azure 中的群集提交作业][jobsubmit]

## <a name="prerequisites"></a>先决条件
* **在 Azure VM 中部署的 HPC Pack 头节点** - 建议使用自动化工具（如 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)）来部署头节点和群集。 需要获得头节点的 DNS 名称和群集管理员的凭据才能完成本文中的步骤。
* **客户端计算机** - 需要有可运行 HPC Pack 客户端实用工具的 Windows 或 Windows Server 客户端计算机（请参阅[系统要求](https://technet.microsoft.com/library/dn535781.aspx)）。 如果只想要使用 HPC Pack Web 门户或 REST API 来提交作业，则可以使用自选的任意客户端计算机。
* **HPC Pack 安装媒体** - 若要安装 HPC Pack 客户端实用工具，可从[下载中心](https://www.microsoft.com/download/details.aspx?id=56360)下载最新版的 HPC Pack 免费安装包。 确保下载与头节点 VM 上安装的版本相同的 HPC Pack 版本。

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>步骤 1：在头节点上安装并配置 Web 组件
要使 REST 接口可通过 HTTPS 将作业提交到群集，请确保在 HPC Pack 头节点上配置了 HPC Pack Web 组件。 若尚未安装，必须先运行 HpcWebComponents.msi 安装文件来安装 Web 组件。 然后，运行 HPC PowerShell 脚本 **Set-HPCWebComponents.ps1**来配置组件。

有关详细过程，请参阅[安装 Microsoft HPC Pack Web 组件](https://technet.microsoft.com/library/hh314627.aspx)。

> [!TIP]
> HPC Pack 群集的某些 Azure 快速入门模板会自动安装并配置 Web 组件。
> 
> 

**安装 Web 组件**

1. 使用群集管理员的凭据连接到头节点 VM。
1. 在头节点上从 HPC Pack 安装程序文件夹中运行 HpcWebComponents.msi。
1. 按照向导中的步骤安装 Web 组件。

**配置 Web 组件**

1. 在头节点上，以管理员身份启动 HPC PowerShell。
1. 要将目录切换到配置脚本所在的位置，请键入以下命令：

    ```powershell
    cd $env:CCP_HOME\bin
    ```
1. 若要配置 REST 接口并启动 HPC Web 服务，请键入以下命令：

    ```powershell
    .\Set-HPCWebComponents.ps1 -Service REST -enable
    ```
1. 在系统提示选择证书时，请选择与头节点的公共 DNS 名称对应的证书。 例如，如果使用经典部署模型部署头节点 VM ，证书名称将类似于 CN=&lt;*HeadNodeDnsName*&gt;.chinacloudapp.cn。 如果使用资源管理器部署模型，证书名称将类似于 CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.chinacloudapi.cn。

   > [!NOTE]
   > 稍后将作业从本地计算机提交到头节点时选择此证书。 不要选择或配置与 Active Directory 域中头节点的计算机名称对应的证书（例如 CN=*MyHPCHeadNode.HpcAzure.local*）。
   > 
   > 
1. 若要配置用于作业提交的 Web 门户，请键入以下命令：

    ```powershell
    .\Set-HPCWebComponents.ps1 -Service Portal -enable
    ```
1. 脚本完成后，请键入以下命令停止并重启 HPC 作业计划程序服务：

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>步骤 2：在本地计算机上安装 HPC Pack 客户端实用工具
若要在计算机上安装 HPC Pack 客户端实用程序，请从[下载中心](https://www.microsoft.com/download/details.aspx?id=56360)下载 HPC Pack 安装程序文件（完整安装）。 开始安装时，请选择针对 **HPC Pack 客户端实用工具**的安装选项。

要使用 HPC Pack 客户端工具向头节点 VM 提交作业，还必须导出头节点中的证书并将其安装在客户端计算机上。 证书必须为 .CER 格式。

**从头节点中导出证书**

1. 在头节点上，向 Microsoft 管理控制台中添加用于“本地计算机”帐户的证书管理单元。 有关添加此管理单元的步骤，请参阅[向 MMC 中添加证书管理单元](https://technet.microsoft.com/library/cc754431.aspx)。
2. 在控制台树中，依次展开“证书 - 本地计算机” > “个人”，然后单击“证书”。
3. 在[步骤 1：在头节点上安装并配置 Web 组件](#step-1-install-and-configure-the-web-components-on-the-head-node)中找到为 HPC Pack Web 组件配置的证书（例如，CN=&lt;*HeadNodeDnsName*&gt;.chinacloudapp.cn）。
   <!-- Notice: Archor IS NOT CONTAIN : -->
4. 右键单击该证书，然后单击“所有任务” > “导出”。
5. 在证书导出向导中，单击“下一步”并确保选中“否，不导出私钥”。
6. 执行此向导中的其余步骤，以“DER 编码二进制 X.509 (.CER)”格式导出证书。

**在客户端计算机上导入证书**

1. 将你从头节点中导出的证书复制到客户端计算机上的某个文件夹中。
1. 在客户端计算机上，运行 certmgr.msc。
1. 在证书管理器中，依次展开“证书 - 当前用户” > “受信任的根证书颁发机构”，右键单击“证书”，然后单击“所有任务” > “导入”。
1. 在证书导入向导中单击“下一步”  ，并按照步骤将从头节点中导出的证书导入“受信任的根证书颁发机构”存储。

> [!TIP]
> 由于客户端计算机未识别头节点上的证书颁发机构，因此可能会出现安全警告。 出于测试目的，可忽略此警告并完成证书导入。
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>步骤 3：在群集上运行测试作业
要验证配置，可以尝试通过本地计算机在 Azure 中的群集上运行作业。 例如，可以使用 HPC Pack GUI 工具或 HPC Pack 命令行命令向群集提交作业。 也可以使用基于 Web 的门户来提交作业。

**在客户端计算机上运行作业提交命令**

1. 在安装了 HPC Pack 客户端实用工具的客户端计算机上，启动命令提示符。
1. 键入示例命令。 例如，若要列出群集中的所有作业，可键入如下所示的某个命令，具体取决于头节点的完整 DNS 名称：

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.chinacloudapp.cn /all
    ```

    或

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.chinacloudapi.cn /all
    ```

   > [!TIP]
   > 请在计划程序 URL 中使用头节点的完整 DNS 名称，而不是 IP 地址。 如果指定 IP 地址，将会显示类似于下面的错误：“服务器证书必须具有有效的信任链，或放置在受信任的根存储区中”。
   > 
   > 
1. 出现提示时，请键入 HPC 群集管理员或配置的另一群集用户的用户名（格式为 &lt;DomainName&gt;\\&lt;UserName&gt;）和密码。 可以选择在本地存储凭据以执行更多作业操作。

    显示作业列表。

**在客户端计算机上使用 HPC 作业管理器**

1. 如果以前在提交作业时未存储群集用户的域凭据，可以在凭据管理器中添加凭据。

    a. 在客户端计算机上的控制面板中，启动凭据管理器。

    b. 单击“Windows 凭据” > “添加普通凭据”。

    c. 指定 Internet 地址（例如， https://&lt;HeadNodeDnsName&gt;.chinacloudapp.cn/HpcScheduler 或 https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.chinacloudapi.cn/HpcScheduler），以及配置的群集管理员或另一群集用户的用户名 (&lt;DomainName&gt;\\&lt;UserName&gt;) 和密码。
1. 在客户端计算机上启动 HPC 作业管理器。
1. 在“选择头节点”对话框中，键入头节点在 Azure 中的 URL（例如， https://&lt;HeadNodeDnsName&gt;.chinacloudapp.cn 或 https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.chinacloudapi.cn）。

    HPC 作业管理器会打开并显示头节点上的作业列表。

**使用在头节点上运行的 Web 门户**

1. 在客户端计算机上启动 Web 浏览器，输入以下任一地址，具体取决于头节点的完整 DNS 名称：

    ```
    https://<HeadNodeDnsName>.chinacloudapp.cn/HpcPortal
    ```

    或

    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.chinacloudapi.cn/HpcPortal
    ```
1. 在出现的安全性对话框中，键入 HPC 群集管理员的域凭据。 （你还可以添加具有不同角色的其他群集用户。 请参阅[管理群集用户](https://technet.microsoft.com/library/ff919335.aspx)。）

    Web 门户会打开并显示作业列表视图。
1. 若要从群集中提交返回“Hello World”字符串的示例作业，请在左侧导航区域中单击“新建作业”  。
1. 在“新建作业”页面上的“从提交页面”下，单击“HelloWorld”。 此时显示作业提交页面。
1. 单击“提交” 。 出现提示时，请提供 HPC 群集管理员的域凭据。 作业已提交，作业 ID 出现在“我的作业”  页面上。
1. 若要查看提交的作业的结果，请单击作业 ID，然后单击“查看任务”查看命令输出（在“输出”下方）。

## <a name="next-steps"></a>后续步骤
* 还可以使用 [HPC Pack REST API](https://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)将作业提交到 Azure 群集。
* 若要从 Linux 客户端提交群集作业，请参阅 [HPC Pack 2012 R2 SDK 和示例代码](https://www.microsoft.com/download/details.aspx?id=41633)中的 Python 示例。

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

<!-- Update_Description: update meta properties, wording update  -->