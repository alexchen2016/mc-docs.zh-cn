---
title: 使用安全地存放在 Azure Stack 上的证书部署虚拟机 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署虚拟机，并使用密钥保管库将证书推送到该虚拟机
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 12/27/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 076b4e1cc1963b89941c456f8422cdfa7dd8436a
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855315"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>创建虚拟机，并安装从 Azure Stack 密钥保管库检索到的证书

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何创建一个安装了密钥保管库证书的 Azure Stack 虚拟机 (VM)。

## <a name="overview"></a>概述

在许多情况下都会使用证书，例如向 Active Directory 进行身份验证或加密 Web 流量。 可以安全地将证书作为机密存储在 Azure Stack 密钥保管库中。 使用 Azure Stack 密钥保管库的好处是：

* 证书不会在脚本、命令行历史记录或模板中公开。
* 简化了证书管理流程。
* 可以控制访问证书的密钥。

### <a name="process-description"></a>过程说明

以下步骤说明将证书推送到虚拟机所需的过程：

1. 创建 Key Vault 机密。
2. 更新 azuredeploy.parameters.json 文件。
3. 部署模板。

> [!NOTE]
> 可以通过 Azure Stack 开发工具包或者外部客户端（如果已通过 VPN 建立连接）执行这些步骤。

## <a name="prerequisites"></a>先决条件

* 必须订阅包含 Key Vault 服务的产品/服务。
* [安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)。
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

## <a name="create-a-key-vault-secret"></a>创建 Key Vault 机密

以下脚本会创建 .pfx 格式的证书、创建密钥保管库，并将该证书作为机密存储在密钥保管库中。

> [!IMPORTANT]
> 创建密钥保管库时，必须使用 `-EnabledForDeployment` 参数。 此参数可确保能够从 Azure 资源管理器模板引用密钥保管库。

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

运行前面的脚本时，输出会包括机密 URI。 请记下此 URI。 在[将证书推送到 Windows 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)中，必须引用此 URI。 将 [vm-push-certificate-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) 模板文件夹下载到开发计算机。 此文件夹包含 `azuredeploy.json` 和 `azuredeploy.parameters.json`文件，在后续步骤中将需要这些文件。

根据环境值，修改 `azuredeploy.parameters.json` 文件。 要注意的参数是保管库名称、保管库资源组和机密 URI（由前面的脚本生成）。 以下部分显示参数文件的示例。

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 文件

根据环境，以 `vaultName`、机密 URI、`VmName` 和其他值更新 `azuredeploy.parameters.json` 文件。 以下 JSON 文件显示模板参数文件的示例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>部署模板

使用以下 PowerShell 脚本部署模板：

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

成功部署模板后，会生成以下输出：

![模板部署结果](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

在部署期间，Azure Stack 会将证书推送到虚拟机。 证书位置取决于 VM 的操作系统：

* 在 Windows 中，系统会利用用户提供的证书存储，将证书添加到 **LocalMachine** 证书位置。
* 在 Linux 中，证书置于 `/var/lib/waagent directory` 下，其中 x509 证书文件的文件名为 &lt;UppercaseThumbprint&gt;.crt，私钥的文件名为 &lt;UppercaseThumbprint&gt;.prv。

## <a name="retire-certificates"></a>停用证书

停用证书是证书管理过程的一部分。 无法删除旧版本的证书，但可以使用 `Set-AzureKeyVaultSecretAttribute` cmdlet 将其禁用。

以下示例说明如何禁用证书。 对 **VaultName**、**Name** 和 **Version** 参数使用自己的值。

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>后续步骤

* [使用密钥保管库密码部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [允许应用程序访问 Key Vault](azure-stack-key-vault-sample-app.md)
