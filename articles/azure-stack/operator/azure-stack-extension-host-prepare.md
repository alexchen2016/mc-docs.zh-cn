---
title: 为 Azure Stack 准备扩展主机 | Microsoft Docs
description: 了解如何准备扩展主机，它是通过将来的一个 Azure Stack 更新程序包自动启用的。
services: azure-stack
keywords: ''
author: WenJason
ms.author: v-jay
origin.date: 03/07/2019
ms.date: 04/29/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: digimobile
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 8423d13e52511263bf1cf6582d70ad404c35e518
ms.sourcegitcommit: 05aa4e4870839a3145c1a3835b88cf5279ea9b32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64529547"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>为 Azure Stack 准备扩展主机

扩展主机通过减少所需的 TCP/IP 端口数来保护 Azure Stack。 本文讨论了为 Azure Stack 准备扩展主机，扩展主机是通过 1808 更新之后的一个 Azure Stack 更新程序包自动启用的。 本文适用于 Azure Stack 更新 1808、1809 和 1811。

## <a name="certificate-requirements"></a>证书要求

扩展主机实施了两个新的域命名空间来为每个门户扩展保证主机条目的唯一性。 新的域命名空间需要两个额外的通配符证书来确保安全的通信。

下表显示了新的命名空间和关联的证书：

| 部署文件夹 | 所需的证书使用者和使用者可选名称 (SAN) | 范围（按区域） | 子域命名空间 |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| 管理扩展主机 | *.adminhosting.\<region>.\<fqdn>（通配符 SSL 证书） | 管理扩展主机 | adminhosting.\<region>.\<fqdn> |
| 公共扩展主机 | *.hosting.\<region>.\<fqdn>（通配符 SSL 证书） | 公共扩展主机 | hosting.\<region>.\<fqdn> |

可以在 [Azure Stack 公钥基础结构证书要求](azure-stack-pki-certs.md)一文中找到详细的证书要求。

## <a name="create-certificate-signing-request"></a>创建证书签名请求

Azure Stack 就绪性检查器工具能够为两个新的必需 SSL 证书创建证书签名请求。 请按照 [Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)一文中的步骤进行操作。

> [!Note]  
> 你可以跳过此步骤，具体取决于你请求 SSL 证书的方式。

## <a name="validate-new-certificates"></a>验证新证书

1. 使用提升的权限在硬件生命周期主机或 Azure Stack 管理工作站上打开 PowerShell。
2. 运行以下 cmdlet 来安装 Azure Stack 就绪性检查器工具。

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. 运行以下脚本来创建必需的文件夹结构：

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > 如果使用 Azure Active Directory 联合身份验证服务 (AD FS) 进行部署，则必须在脚本中的 **$directories** 中添加以下目录：`ADFS`、`Graph`。

4. 将现有的证书（目前在 Azure Stack 中使用）置于相应的目录中。 例如，将 **Admin ARM** 证书置于 `Arm Admin` 文件夹中。 然后，将新创建的托管证书置于 `Admin extension host` 和 `Public extension host` 目录中。
5. 运行以下 cmdlet 来启动证书检查：

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. 检查输出和所有证书是否通过所有测试。


## <a name="import-extension-host-certificates"></a>导入扩展主机证书

使用可以连接到 Azure Stack 特权终结点的计算机执行后续步骤。 请确保可以从该计算机访问新的证书文件。

1. 使用可以连接到 Azure Stack 特权终结点的计算机执行后续步骤。 请确保可以从该计算机访问新的证书文件。
2. 打开 PowerShell ISE 以执行接下来的脚本块
3. 导入用于管理托管终结点的证书。

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. 导入用于托管终结点的证书。
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>更新 DNS 配置

> [!Note]  
> 如果使用了 DNS 区域委派进行 DNS 集成，则此步骤不是必需的。
如果已配置了单独的主机 A 记录来发布 Azure Stack 终结点，则需要创建两个额外的主机 A 记录：

| IP | 主机名 | 类型 |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | A |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | A |

可以通过运行 cmdlet **Get-AzureStackStampInformation** 使用特权终结点检索已分配的 IP。

### <a name="ports-and-protocols"></a>端口和协议

[Azure Stack 数据中心集成 - 发布终结点](azure-stack-integrate-endpoints.md)一文介绍了在推出扩展主机之前进行入站通信以发布 Azure Stack 所需的端口和协议。

### <a name="publish-new-endpoints"></a>发布新的终结点

需要通过防火墙发布两个新的终结点。 可以使用以下代码从公共 VIP 池检索已分配的 IP，该代码必须通过 Azure Stack [环境的特权终结点](azure-stack-privileged-endpoint.md)运行。

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>示例输出

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> 请在启用扩展主机前进行此更改。 这使得 Azure Stack 门户持续可访问。

| 终结点 (VIP) | 协议 | 端口 |
|----------------|----------|-------|
| 管理员托管 | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>更新现有的发布规则（在启用扩展主机后）

> [!Note]  
> 1808 Azure Stack 更新包**尚未**启用扩展主机。 它允许通过导入所需的证书来准备扩展主机。 在通过 1808 更新之后的 Azure Stack 更新包自动启用扩展主机之前，请不要关闭任何端口。

在现有的防火墙规则中，必须关闭以下终结点端口。

> [!Note]  
> 建议在成功验证后关闭这些端口。

| 终结点 (VIP) | 协议 | 端口 |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| 门户（管理员） | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| 门户（用户） | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure 资源管理器（管理员） | HTTPS | 30024 |
| Azure 资源管理器（用户） | HTTPS | 30024 |

## <a name="next-steps"></a>后续步骤

- 了解[防火墙集成](azure-stack-firewall.md)。
- 了解 [Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)
