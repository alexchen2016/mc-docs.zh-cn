---
title: Azure Service Fabric 基础结构即代码最佳做法 | Azure
description: 用于管理 Service Fabric 基础结构即代码的最佳做法。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 01/23/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 841f24ffe4c631ae8ba19afcfb3c9366461021d8
ms.sourcegitcommit: ea33f8dbf7f9e6ac90d328dcd8fb796241f23ff7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57204253"
---
<!--Verify successfully-->
# <a name="infrastructure-as-code"></a>基础结构即代码

在生产方案中，使用资源管理器模板创建 Azure Service Fabric 群集。 资源管理器模板可以更好地控制资源属性，并确保你具有一致的资源模型。

[GitHub 上的 Azure 示例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中提供了适用于 Windows 和 Linux 的示例资源管理器模板。 这些模板可用作群集模板的起点。 下载 `azuredeploy.json` 和 `azuredeploy.parameters.json` 并编辑它们以满足你的自定义要求。

> [!NOTE]
> 必须修改从 GitHub 存储库“Azure-Samples”下载或引用的模板，使之适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”）；必要时更改某些不受支持的 VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

> [!NOTE]
> 例如，当我们尝试在 Azure 中安装 [5 节点安全 Windows Service Fabric 群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)时。
> 在成功下载相应的模板文件后，我们应当替换以下配置来满足 Azure 中国环境：
> * 替换 [azuredeploy.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.json) 中的 storageAccountEndPoint。
>     * 将 `"storageAccountEndPoint": "https://core.windows.net/"` 替换为 `"storageAccountEndPoint": "https://core.chinacloudapi.cn/"`。
> * 替换 [azuredeploy.parameters.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.Parameters.json) 中的 clusterLocation。
>     * 将 `WestUS` 替换为 `chinanorth`。
> * 替换 [New-ServiceFabricClusterCertificate.ps1](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/New-ServiceFabricClusterCertificate.ps1) 中的 Location。
>     * 将 `WestUS` 替换为 `chinanorth`。

若要部署你在上面下载的 `azuredeploy.json` 和 `azuredeploy.parameters.json` 模板，请使用以下 Azure CLI 命令：

```azurecli
ResourceGroupName="sfclustergroup"
Location="chinanorth"
DeploymentName="yourdeploymentname"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $DeploymentName --resource-group $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

使用 PowerShell 创建资源

```powershell
$ResourceGroupName="sfclustergroup"
$Location="chinanorth"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"
$DeploymentName="yourdeploymentname"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric 资源

可以通过 Azure 资源管理器，将应用程序和服务部署到 Service Fabric 群集。 有关详细信息，请参阅[将应用程序和服务作为 Azure 资源管理器资源进行管理](/service-fabric/service-fabric-application-arm-resource)。 下面是要在资源管理器模板资源中包括的最佳做法 Service Fabric 应用程序特定资源。

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

若要使用 Azure 资源管理器部署应用程序，必须首先[创建一个 sfpkg](/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric 应用程序包。 下面的 python 脚本是有关如何创建 sfpkg 的示例：

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

<!--Update_Description: new articles on service fabric best practices infrastructure as code -->
<!--ms.date: 03/04/2019-->