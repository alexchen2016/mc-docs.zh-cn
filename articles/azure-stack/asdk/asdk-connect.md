---
title: 连接到 Azure Stack | Microsoft Docs
description: 了解如何连接到 ASDK。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/06/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 1743c406977e92a657b4d8ff5e35bf24bae1d359
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381839"
---
# <a name="connect-to-the-asdk"></a>连接到 ASDK

若要管理资源，必须先连接到 Azure Stack 开发工具包 (ASDK)。 本文介绍使用以下连接选项连接到 ASDK 所要执行的步骤：

* [远程桌面连接 (RDP)](#connect-with-rdp)。 使用远程桌面连接进行连接时，单个用户可以快速连接到开发工具包。
* [虚拟专用网络 (VPN)](#connect-with-vpn)。 使用 VPN 进行连接时，多个用户可以同时从 Azure Stack 基础结构外部的客户端连接到 Azure Stack 门户。 VPN 连接需要一些设置。

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>使用 RDP 连接到 Azure Stack

单个并发用户可以在 Azure Stack 管理门户或用户门户中，通过远程桌面连接直接从 ASDK 主机管理资源。 

> [!TIP]
> 此选项还可让你在已登录到 ASDK 主机的情况下，再次使用 RDP 登录到在 ASDK 主机上创建的虚拟机。 

1. 打开远程桌面连接 (mstc.exe)，然后使用有权远程登录到 ASDK 主机的帐户连接到开发工具包主机 IP 地址。 默认情况下，**AzureStack\AzureStackAdmin** 有权远程登录到 ASDK 主机。  

2. 在开发工具包主机上，打开服务器管理器 (ServerManager.exe)。 选择“本地服务器”，禁用“IE 增强的安全配置”，然后关闭服务器管理器。  

3. 以 **AzureStack\CloudAdmin** 身份或使用其他 Azure Stack 操作员凭据登录到管理门户。 ASDK 管理门户地址为 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)。

4. 以 **AzureStack\CloudAdmin** 身份或使用其他 Azure Stack 用户凭据登录到用户门户。 ASDK 用户门户地址为 [https://portal.local.azurestack.external](https://portal.local.azurestack.external)。

> [!NOTE]
> 有关何时要使用哪个帐户的详细信息，请参阅 [ASDK 管理基础知识](asdk-admin-basics.md#what-account-should-i-use)。

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>使用 VPN 连接到 Azure Stack

可与 ASDK 主机建立拆分隧道 VPN 连接，以访问 Azure Stack 门户和本地安装的工具（例如 Visual Studio 和 PowerShell）。 多个用户可以使用 VPN 连接同时连接到 ASDK 托管的 Azure Stack 资源。

Azure AD 部署和 Active Directory 联合身份验证服务 (AD FS) 部署都支持 VPN 连接。

> [!NOTE]
> 使用 VPN 无法连接到 Azure Stack VM。  通过 VPN 建立连接时，无法使用 RDP 连接到 Azure Stack VM。

### <a name="prerequisites"></a>先决条件
在设置 ASDK 的 VPN 连接之前，请确保符合以下先决条件。

- 在本地计算机上安装[与 Azure Stack 兼容的 Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)。  
- 下载[使用 Azure Stack 所需的工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

### <a name="set-up-vpn-connectivity"></a>设置 VPN 连接

若要与 ASDK 建立 VPN 连接，请在基于 Windows 的本地计算机上，以管理员身份打开 PowerShell。 然后，运行以下脚本（更新环境的 IP 地址和密码值）：

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果设置成功，**Azure Stack** 将出现在 VPN 连接列表中。

![网络连接](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

  使用以下方法之一连接到 Azure Stack 实例：  

  * 使用 `Connect-AzsVpn` 命令：
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * 在本地计算机上，选择“网络设置”   > “VPN”   > “Azure Stack”   > “连接”  。 在登录提示符下，输入用户名 (**AzureStack\AzureStackAdmin**) 和密码。

首次连接时，系统会提示在本地计算机的证书存储中安装来自 **AzureStackCertificateAuthority** 的 Azure Stack 根证书。 此步骤将 ASDK 证书颁发机构 (CA) 添加到受信任的主机列表。 单击“是”以安装证书。 

![根证书](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > 提示可能会被 PowerShell 窗口或其他应用程序遮挡。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接

若要测试门户连接，请打开 Web 浏览器，然后转到用户门户 (https://portal.local.azurestack.external/) 或管理门户 (https://adminportal.local.azurestack.external/)。 

使用相应的订阅凭据登录，以创建和管理资源。  

## <a name="next-steps"></a>后续步骤

[故障排除](asdk-troubleshooting.md)
