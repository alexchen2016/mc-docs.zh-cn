---
title: 在 Azure 中创建 Linux Service Fabric 群集 | Azure
description: 了解如何使用 Azure CLI 将 Linux Service Fabric 群集部署到现有 Azure 虚拟网络。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/14/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 1322efad88bfadfb2c70b4673c14f6de75a8b9b4
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625705"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>将 Linux Service Fabric 群集部署到 Azure 虚拟网络

本文介绍了如何使用 Azure CLI 和模板将 Linux Service Fabric 群集部署到 [Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md) 中。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。 若要使用 PowerShell 创建 Windows 群集，请参阅[在 Azure 上创建安全的 Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。

## <a name="prerequisites"></a>先决条件

开始之前：

* 如果还没有 Azure 订阅，请创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)
* 安装 [Service Fabric CLI](service-fabric-cli.md)
* 安装 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)
* 若要了解群集的关键概念，请阅读 [Azure 群集概述](service-fabric-azure-clusters-overview.md)

以下步骤将创建一个七节点 Service Fabric 群集。 若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://www.azure.cn/pricing/calculator/)。

## <a name="download-and-explore-the-template"></a>下载并浏览模板

下载以下资源管理器模板文件：

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

> [!NOTE]
> 必须修改从 GitHub 存储库“Azure-Samples”下载或引用的模板，使之适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”）；必要时更改某些不受支持的位置、VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

<!--Notice: Change storageAccountEndPoint as https://core.chinacloudapi.cn/-->

> [!NOTE]
> 对于本文，在成功下载相应的文件后，我们应当替换以下配置来满足 Azure 中国环境：
> * 在 [AzureDeploy.json][template] 中三次替换 storageAccountEndPoint。
>     * 将 `"storageAccountEndPoint": "https://core.windows.net/"` 替换为 `"storageAccountEndPoint": "https://core.chinacloudapi.cn/"`。
> * 替换 [AzureDeploy.Parameters.json][parameters] 中的 clusterLocation。
>     * 将 `westus` 替换为 `chinanorth`。
> * 替换 [New-ServiceFabricClusterCertificate.ps1](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/New-ServiceFabricClusterCertificate.ps1) 中的 Location。
>     * 将 `WestUS` 替换为 `chinanorth`。

此模板将包含七个虚拟机和三个节点类型的安全群集部署到虚拟网络中。  其他示例模板可以在 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) 上找到。 [AzureDeploy.json][template] 部署一些资源，包括以下资源。

### <a name="service-fabric-cluster"></a>Service Fabric 群集

在 **Microsoft.ServiceFabric/clusters** 资源中，部署了具有以下特征的 Linux 群集：

* 三个节点类型
* 主节点类型包含五个节点（可在模板参数中配置），其他节点类型各包含一个节点
* OS：Ubuntu 16.04 LTS（可在模板参数中配置）
* 证书保护（可在模板参数中配置）
* 已启用 [DNS 服务](service-fabric-dnsservice.md)
* 铜级[持久性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)（可在模板参数中配置）
* 银级[可靠性级别](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)（可在模板参数中配置）
* 客户端连接终结点：19000（可在模板参数中配置）
* HTTP 网关终结点：19080（可在模板参数中配置）

### <a name="azure-load-balancer"></a>Azure 负载均衡器

在 **Microsoft.Network/loadBalancers** 资源中，配置了负载均衡器，并为以下端口设置了探测和规则：

* 客户端连接终结点：19000
* HTTP 网关终结点：19080
* 应用程序端口：80
* 应用程序端口：443

### <a name="virtual-network-and-subnet"></a>虚拟网络和子网

虚拟网络和子网的名称在模板参数中声明。  虚拟网络和子网的地址空间也在模板参数中声明，并在 **Microsoft.Network/virtualNetworks** 资源中配置：

* 虚拟网络地址空间：10.0.0.0/16
* Service Fabric 子网地址空间：10.0.2.0/24

如需其他任何应用程序端口，则需要调整 Microsoft.Network/loadBalancers 资源，以允许传入流量。

## <a name="set-template-parameters"></a>设置模板参数

[AzureDeploy.Parameters][parameters] 参数文件声明用于部署群集和关联资源的多个值。 可能需要使用某些参数来修改部署：


|       参数       | 示例值  |                                                                                                                                                                          说明                                                                                                                                                                           |
|-----------------------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     adminUserName     |    vmadmin     |                                                                                                                                                           群集 VM 的管理员用户名。                                                                                                                                                            |
|     adminPassword     | Password#1234  |                                                                                                                                                           群集 VM 的管理员密码。                                                                                                                                                            |
|      clusterName      | mysfcluster123 |                                                                                                                                                                   群集的名称。                                                                                                                                                                   |
|       location        |   chinaeast    |                                                                                                                                                                 群集的位置。                                                                                                                                                                 |
| certificateThumbprint |                |                                  <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到密钥保管库的现有证书，请填写证书 SHA1 指纹值。 例如“6190390162C988701DB5676EB81083EA608DCCF3”。 </p>                                   |
|  certificateUrlValue  |                |                 <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写证书 URL。 例如“<https://mykeyvault.vault.azure.cn:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346>”。</p>                 |
|   sourceVaultValue    |                | <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写源保管库值。 例如“/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”。</p> |

<a name="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>部署虚拟网络和群集

接下来，设置网络拓扑并部署 Service Fabric 群集。 [AzureDeploy.json][template] 资源管理器模板为 Service Fabric 创建虚拟网络 (VNET) 和子网。 该模板还会部署一个已启用证书安全性的群集。  对于生产群集，请使用证书颁发机构 (CA) 提供的证书作为群集证书。 可以使用自签名证书来保护测试群集。

本文中的模板部署一个群集，该群集使用证书指纹来标识群集证书。  两个证书不能有相同的指纹，否则会增加证书管理的难度。 将已部署的群集从使用证书指纹切换为使用证书公用名称会使证书管理更加简单。  若要了解如何更新群集，以便使用证书公用名称进行证书管理，请阅读[将群集更改为使用证书公用名称进行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

### <a name="create-a-cluster-using-an-existing-certificate"></a>使用现有证书创建群集

以下脚本使用 [az sf cluster create](https://docs.azure.cn/zh-cn/cli/sf/cluster?view=azure-cli-latest#az-sf-cluster-create) 命令和模板部署一个以现有证书保护的新群集。 该命令还会在 Azure 中创建新的 Key Vault，并上传证书。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="chinaeast"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>使用新的自签名证书创建群集

以下脚本使用 [az sf cluster create](https://docs.azure.cn/zh-cn/cli/sf/cluster?view=azure-cli-latest#az-sf-cluster-create) 命令和模板在 Azure 中部署新群集。 此命令还会在 Azure 中创建新的密钥保管库、向密钥保管库添加新的自签名证书，并将证书文件下载到本地。

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="chinaeast"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.chinacloudapi.cn --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>连接到安全群集

使用密钥通过 Service Fabric CLI 命令 `sfctl cluster select` 连接到群集。  请注意仅针对自签名证书使用 **--no-verify** 选项。

```azurecli
sfctl cluster select --endpoint https://aztestcluster.chinaeast.cloudapp.chinacloudapi.cn:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

检查是否已连接并使用 `sfctl cluster health` 命令检查群集是否处于正常状态。

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>清理资源

如果不立即转到下一篇文章，可能需要[删除该群集](service-fabric-cluster-delete.md)，以避免产生费用。

## <a name="next-steps"></a>后续步骤

了解如何[缩放群集](service-fabric-tutorial-scale-cluster.md)。

本文中的模板部署一个群集，该群集使用证书指纹来标识群集证书。  两个证书不能有相同的指纹，否则会增加证书管理的难度。 将已部署的群集从使用证书指纹切换为使用证书公用名称会使证书管理更加简单。  若要了解如何更新群集，以便使用证书公用名称进行证书管理，请阅读[将群集更改为使用证书公用名称进行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json

<!--Update_Description: update link, wording update -->