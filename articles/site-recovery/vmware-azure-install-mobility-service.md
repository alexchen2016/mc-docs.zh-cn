---
title: 安装移动服务（VMware/物理机到 Azure）| Azure
description: 了解如何安装移动服务代理以使用 Azure Site Recovery 保护本地 VMware VM 和物理服务器。
author: rockboyfor
ms.service: site-recovery
ms.topic: article
origin.date: 07/06/2018
ms.date: 09/17/2018
ms.author: v-yeche
ms.openlocfilehash: 33b36325dd20b9421a82d7febd9d173c8d01a872
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643772"
---
# <a name="install-the-mobility-service"></a>安装移动服务 

Azure Site Recovery 移动服务安装在要复制到 Azure 的 VMware VM 和物理服务器上。 该服务捕获计算机上的数据写入，然后将其转发到进程服务器。 将移动服务部署到要复制到 Azure 的每台计算机（VMware VM 或物理服务器）上。 可使用以下方法将移动服务部署到需要保护的服务器和 VMware VM 上：

* [使用 System Center Configuration Manager 等软件部署工具安装](vmware-azure-mobility-install-configuration-mgr.md)
* [使用 Azure 自动化和 Desired State Configuration (Automation DSC) 安装](vmware-azure-mobility-deploy-automation-dsc.md)
* [从 UI 手动安装](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [从命令提示符手动安装](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [使用 Site Recovery 推送安装进行安装](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)

>[!IMPORTANT]
> 从版本 9.7.0.0 开始，在 Windows VM 上，移动服务安装程序还会安装最新可用的 [Azure VM 代理](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 这可确保当计算机故障转移到 Azure 时，它满足使用任何 VM 扩展的代理安装先决条件。
> </br>在 Linux VM 上，必须手动安装 WALinuxAgent。

## <a name="prerequisites"></a>先决条件
在服务器上手动安装移动服务之前，请先完成以下必要步骤：
1. 登录配置服务器，并以管理员身份打开“命令提示符”窗口。
2. 将目录更改到 bin 文件夹，然后创建一个密码文件。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 将密码文件存储在安全位置中。 安装移动服务时需要使用此文件。
4. 适用于所有支持的操作系统的移动服务安装程序均位于 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 文件夹。

### <a name="mobility-service-installer-to-operating-system-mapping"></a>移动服务安装程序到操作系统的映射

要查看具有兼容移动服务包的操作系统列表，请参阅 [VMware 虚拟机和物理服务器支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。

| 安装程序文件模板名称| 操作系统 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1（64 位） </br> Windows Server 2012（64 位） </br> Windows Server 2012 R2（64 位） </br> Windows Server 2016（64 位） |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3（仅限 64 位）|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4（仅限 64 位）|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5（仅限 64 位）|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04（仅限 64 位）|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 服务器（仅限 64 位）|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7（仅限 64 位）|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8（仅限 64 位）|
<!-- Not Avaiable on Red Hat Enterprise Linux (RHEL) -->

## <a name="install-mobility-service-manually-by-using-the-gui"></a>使用 GUI 手动安装移动服务

>[!IMPORTANT]
> 如果要使用配置服务器将 Azure IaaS 虚拟机从一个 Azure 订阅/区域复制到另一个 Azure 订阅/区域，请使用基于命令行的安装方法。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>在命令提示符下手动安装移动服务

### <a name="command-line-installation-on-a-windows-computer"></a>Windows 计算机上的命令行安装
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Linux 计算机上基于命令行的安装
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]

## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>在 Azure Site Recovery 中使用推送安装安装移动服务
可以使用 Site Recovery 执行移动服务的推送安装。 所有目标计算机都必须满足以下先决条件。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]

> [!NOTE]
安装移动服务后，在 Azure 门户中选择“+复制”以开始保护这些 VM。

## <a name="update-mobility-service"></a>更新移动服务

> [!WARNING]
> 开始在受保护的服务器上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。

1. 在 Azure 门户中，浏览到 *你的保管库的名称* > “复制的项”视图。
2. 如果配置服务器已更新到最新版本，则会看到一条通知，指出“新的 Site Recovery 复制代理更新已可用。 单击可安装。”

     ![“复制的项”窗口](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. 选择通知以打开虚拟机选择页面。
4. 选择要升级移动服务的虚拟机，然后选择“确定”。

     ![“复制的项”VM 列表](.\media\vmware-azure-install-mobility-service\update-okpng.png)

将为所选的每个虚拟机启动“更新移动服务”作业。

> [!NOTE]
> [详细了解](vmware-azure-manage-configuration-server.md)如何为用于安装移动服务的帐户更新密码。

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>卸载 Windows Server 计算机上的移动服务
通过以下方法之一卸载 Windows Server 计算机上的移动服务。

### <a name="uninstall-by-using-the-gui"></a>使用 GUI 卸载
1. 在控制面板中，选择“程序” 。
2. 选择“Azure Site Recovery 移动服务/主目标服务器”，并单击“卸载”。

### <a name="uninstall-at-a-command-prompt"></a>在命令提示符下卸载
1. 以管理员身份打开“命令提示符”窗口。
2. 若要卸载移动服务，请运行以下命令：

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>卸载 Linux 计算机上的移动服务
1. 以 **root** 用户身份在 Linux 服务器上登录。
2. 在终端中转到 /user/local/ASR。
3. 若要卸载移动服务，请运行以下命令：

    ```
    uninstall.sh -Y
    ```

<!-- Update_Description: update meta properties, wording update  -->