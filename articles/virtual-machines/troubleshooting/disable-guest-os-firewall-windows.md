---
title: 在 Azure VM 中禁用来宾 OS 防火墙 | Azure
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
origin.date: 11/22/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 01f8202e6825e22f3cadada0768a65333e3b5a80
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003947"
---
<!-- Verify part successfully-->
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>在 Azure VM 中禁用来宾 OS 防火墙

本文为以下情况提供参考：你怀疑来宾操作系统防火墙正在筛选发往虚拟机 (VM) 的部分或全部流量。 如果故意对导致 RDP 连接失败的防火墙进行更改，则可能发生这种情况。

## <a name="solution"></a>解决方案

本文所述的过程（即，如何正确设置防火墙规则）旨在作为一种解决方法，让你能够集中精力解决实际问题。 它是一种启用 Windows 防火墙组件的 Azure 最佳做法。 如何配置防火墙规则取决于对 VM 所需的访问级别。

### <a name="online-solutions"></a>联机解决方案 

如果该 VM 处于联机状态且可以在同一虚拟网络中的另一个 VM 上对其进行访问，则可以使用另一个 VM 执行以下缓解措施。

#### <a name="mitigation-1-custom-script-extension-feature"></a>缓解措施 1：自定义脚本扩展功能

<!-- Not Available on Run Command feature-->

如果有正在工作的 Azure 代理，则可以使用[自定义脚本扩展](../extensions/custom-script-windows.md)来远程运行以下脚本。

<!-- Not Available on [Run Commands](../windows/run-command.md)-->

> [!Note]
> * 如果在本地设置防火墙，请运行以下脚本：
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * 如果通过 Active Directory 策略设置防火墙，则可以运行以下脚本进行临时访问。 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   但是，只要再次应用该策略，就会被踢出远程会话。 此问题的永久性解决方法是修改此计算机上应用的策略。

<!--Not Available on #### Mitigation 2: Remote PowerShell-->

#### <a name="mitigation-2-pstools-commands"></a>缓解措施 2：PSTools 命令

1.  在故障排除 VM 上，下载 [PSTools](https://docs.microsoft.com/zh-cn/sysinternals/downloads/pstools)。

2.  打开 CMD 实例，然后通过其 DIP 访问 VM。

3.  运行以下命令：

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-3-remote-registry"></a>缓解措施 3：远程注册表 

按以下步骤来使用[远程注册表](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)。

1.  在故障排除 VM 上，启动注册表编辑器，然后转到“文件” > “连接网络注册表”。

2.  打开  *TARGET MACHINE*\SYSTEM 分支，指定以下值：

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  重启服务。 由于无法使用远程注册表执行此操作，因此必须使用“删除服务控制台”。

4.  打开  **Services.msc** 的实例。

5.  单击“服务(本地)”。

6.  选择“连接到另一台计算机”。

7.  输入问题 VM 的 **专用 IP 地址 (DIP)** 。

8.  重启本地防火墙策略。

9.  尝试从本地计算机再次通过 RDP 连接到该 VM。

### <a name="offline-solutions"></a>脱机解决方案 

如果遇到无法通过任何方法访问该 VM 的情况，则自定义脚本扩展将失败，你必须直接通过系统磁盘在脱机模式下工作。 为此，请执行以下步骤：

1.  [将系统磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  开始与恢复 VM 建立远程桌面连接。

3.  确保磁盘在磁盘管理控制台中标记为“联机”。 请留意分配给附加系统磁盘的驱动器号。

4.  在进行任何更改之前，请创建 \windows\system32\config 文件夹的副本，以防需要回滚更改。

5.  在故障排除 VM 上，启动注册表编辑器 (regedit.exe)。 

6.  对于此故障排除过程，我们将配置单元装载为 BROKENSYSTEM 和 BROKENSOFTWARE。

7.  突出显示 HKEY_LOCAL_MACHINE 项，然后从菜单中选择“文件”>“加载配置单元”。

8.  在附加的系统磁盘上找到 \windows\system32\config\SYSTEM 文件。

9.  打开提升的 PowerShell 实例，然后运行以下命令：

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [拆离系统磁盘并重新创建 VM](troubleshoot-recovery-disks-portal-windows.md)。

11. 检查是否解决了问题。

<!-- Update_Description: wording update -->
