---
title: 创建使用证书公用名称的 Azure Service Fabric 群集 | Azure
description: 了解如何基于模板创建使用证书公用名称的 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 04/24/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 4db9a537a597b2b6643851794f64d18a05812fa5
ms.sourcegitcommit: ea33f8dbf7f9e6ac90d328dcd8fb796241f23ff7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57204182"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>部署使用证书公用名称而非指纹的 Service Fabric 群集
两个证书不能具有相同的指纹，具有相同的指纹会使群集证书滚动更新或管理变得困难。 但是，多个证书可以具有相同的公用名称或使用者。  使用证书公用名称会使群集的证书管理更加简单。 本文介绍了如何部署 Service Fabric 群集来使用证书公用名称而非证书指纹。

## <a name="get-a-certificate"></a>获取证书
首先，从[证书颁发机构 (CA)](https://wikipedia.org/wiki/Certificate_authority) 获取证书。  证书的公用名称应该是针对你拥有的自定义域，并且是从域注册机构购买的。 例如，“azureservicefabricbestpractices.com”；不是 Azure 员工的用户不能为 MS 域预配证书，因此不能使用 LB 或流量管理器的 DNS 名称作为证书的公用名称，而需预配 [Azure DNS 区域](/dns/dns-delegate-domain-azure-dns)（前提是自定义域可以在 Azure 中解析）。 如果希望门户反映群集的自定义域别名，则还需将拥有的自定义域声明为群集的“managementEndpoint”。

对于测试用途，可以从免费或开放的证书颁发机构获取由 CA 签名的证书。

> [!NOTE]
> 不支持自签名证书，包括在 Azure 门户中部署 Service Fabric 群集时生成的证书。 

## <a name="upload-the-certificate-to-a-key-vault"></a>将证书上传到密钥保管库中
在 Azure 中，Service Fabric 群集部署在虚拟机规模集上。  将证书上传到密钥保管库中。  在群集部署时，证书将安装在运行群集的虚拟机规模集上。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -Environment AzureChinaCloud -SubscriptionId $SubscriptionId

$region = "chinaeast"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>下载并更新示例模板
本文使用了 [5 节点安全群集示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)模板和模板参数。 将 *azuredeploy.json* 和 *azuredeploy.parameters.json* 文件下载到计算机。

> [!NOTE]
> 必须修改从 GitHub 存储库“Azure-Samples”下载或引用的模板，使之适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”）；必要时更改某些不受支持的位置、VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

<!--Notice: Change storageAccountEndPoint as https://core.chinacloudapi.cn/-->

> [!NOTE]
> 在成功下载相应的模板文件后，我们应当替换以下配置来满足 Azure 中国环境：
> * 替换 [azuredeploy.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.json) 中的 storageAccountEndPoint。
>     * 将 `"storageAccountEndPoint": "https://core.windows.net/"` 替换为 `"storageAccountEndPoint": "https://core.chinacloudapi.cn/"`。
> * 替换 [azuredeploy.parameters.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.Parameters.json) 中的 clusterLocation。
>     * 将 `WestUS` 替换为 `chinanorth`。
> * 替换 [New-ServiceFabricClusterCertificate.ps1](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/New-ServiceFabricClusterCertificate.ps1) 中的 Location。
>     * 将 `WestUS` 替换为 `chinanorth`。

### <a name="update-parameters-file"></a>更新参数文件
首先，在文本编辑器中打开 *azuredeploy.parameters.json* 文件并添加以下参数值：
```json
"certificateCommonName": {
    "value": "myclustername.chinaeast.cloudapp.chinacloudapi.cn"
},
```

接下来，将 *certificateCommonName*、*sourceVaultValue* 和 *certificateUrlValue* 参数值设置为前面的脚本返回的值：
```json
"certificateCommonName": {
    "value": "myclustername.chinaeast.cloudapp.chinacloudapi.cn"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.cn:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>更新模板文件
接下来，在文本编辑器中打开 *azuredeploy.json* 文件并进行三项更新以支持证书公用名称。

1. 在 **parameters** 部分中，添加 *certificateCommonName* 参数：
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    另请考虑删除 *certificateThumbprint*，可能不再需要此项。

2. 将 *sfrpApiVersion* 变量的值设置为“2018-02-01”：
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. 在 **Microsoft.Compute/virtualMachineScaleSets** 资源中，更新虚拟机扩展以在证书设置中使用公用名称而非指纹。  在“virtualMachineProfile”->“extensionProfile”->“扩展”->“属性”->“设置”->“证书”中，添加 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    并删除 `"thumbprint": "[parameters('certificateThumbprint')]",`。

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  在 **Microsoft.ServiceFabric/clusters** 资源中，将 API 版本更新为“2018-02-01”。  另请添加包含 **commonNames** 属性的 **certificateCommonNames** 设置，并删除 **certificate** 设置（包含指纹属性），如以下示例中所示：
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```
    
    > [!NOTE]
    > 可以使用“certificateIssuerThumbprint”字段通过给定的使用者公用名指定证书的预期颁发者。 此字段接受以逗号分隔的 SHA1 指纹枚举。 请注意，这是对证书验证的加强 - 在未指定颁发者或颁发者为空的情况下，如果可以构建证书的链并最终得到验证程序信任的根证书，则会接受该证书以用于身份验证。 当指定了颁发者时，如果直接颁发者的指纹与此字段中指定的任意值匹配，则无论根证书是否受信任，都会接受该证书。 请注意，PKI 可以使用不同的证书颁发机构来为同一使用者颁发证书，因此，为给定的使用者指定所有预期的颁发者指纹非常重要。
    >
    > 指定颁发者被认为是最佳做法，但对于可以链接成受信任的根证书的证书，省略颁发者也是可行的，此行为存在限制，在不久的将来可能会被淘汰。 另请注意，如果在 Azure 中部署的群集受由某个专用 PKI 颁发且通过使用者声明的 X509 证书保护，并且该 PKI 的证书策略不可发现、不可用且无法访问，则 Azure Service Fabric 服务可能无法对群集进行验证（对于群集到服务通信）。 

## <a name="deploy-the-updated-template"></a>部署已更新的模板
在进行更改后，重新部署已更新的模板。

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="chinaeast"  
$id="<subscription ID>"
$deploymentname="yourdeploymentname"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -Environment AzureChinaCloud -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -Name $deploymentname -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>后续步骤
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解如何[滚动更新群集证书](service-fabric-cluster-rollover-cert-cn.md)
* [更新和管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
* 通过[将群集从证书指纹更改为通用名称](service-fabric-cluster-change-cert-thumbprint-to-cn.md)来简化证书管理

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png

<!-- Update_Description: wording update, update meta properties -->
