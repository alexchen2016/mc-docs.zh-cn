---
title: 创建 Azure 应用程序网关 - 模板
description: 本文提供了有关使用 Azure 资源管理器模板创建 Azure 应用程序网关的说明
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
origin.date: 04/15/2019
ms.date: 05/20/2019
ms.author: v-junlch
ms.openlocfilehash: b6071900e695dd4bb9f5455a72efccdd84364610
ms.sourcegitcommit: dc0db00da570f0c57f4a1398797fc158a2c423c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65960916"
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建应用程序网关

Azure 应用程序网关是第 7 层负载均衡器。 它可在云端或本地​​的服务器间提供故障转移和性能路由 HTTP 请求。 应用程序网关提供许多应用程序传送控制器 (ADC) 功能，包括 HTTP 负载均衡、基于 Cookie 的会话相关性、安全套接字层 (SSL) 卸载、自定义运行状况探测、多站点支持，以及许多其他功能。 若要查找受支持功能的完整列表，请访问[应用程序网关概述](application-gateway-introduction.md)

本文介绍如何从 GitHub 下载并修改现有 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)，以及如何通过 GitHub、PowerShell 和 Azure CLI 部署该模板。

如果只是直接从 GitHub 部署模板，而不进行任何更改，请跳到“从 GitHub 部署模板”。

## <a name="scenario"></a>方案

在此方案中，将要：

* 创建具有 Web 应用程序防火墙的应用程序网关。
* 创建名为 VirtualNetwork1 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。
* 为需要用来进行流量负载均衡的 Web 服务器设置两个先前已配置的后端 IP。 在此模板示例中，后端 IP 是 10.0.1.10 和 10.0.1.11。

> [!NOTE]
> 这些设置是适用于此模板的参数。 若要自定义模板，可更改 azuredeploy.json 文件中的规则、侦听程序、SSL 以及其他选项。

![方案](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>下载并了解 Azure Resource Manager 模板

可以从 GitHub 下载用于创建虚拟网络和两个子网的现有 Azure Resource Manager 模板，进行任何所需的更改，并重用该模板。 为此，请使用以下步骤：

1. 导航到[创建启用了 Web 应用程序防火墙的应用程序网关](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf)。
1. 单击 **azuredeploy.json**，然后单击 **RAW**。
1. 将该文件保存到计算机上的本地文件夹中。
1. 如果熟悉 Azure Resource Manager 模板，则跳到步骤 7。
1. 打开保存的文件，并查看 **parameters** 下行中的内容
1. Azure Resource Manager 模板参数提供了在部署过程中可以填充的值的占位符。

   | 参数 | 说明 |
   | --- | --- |
   | **subnetPrefix** |应用程序网关子网的 CIDR 块。 |
   | **applicationGatewaySize** | 应用程序网关的大小。  WAF 仅允许中型和大型网关。 |
   | **backendIpaddress1** |第一个 Web 服务器的 IP 地址。 |
   | **backendIpaddress2** |第二个 Web 服务器的 IP 地址。 |
   | **wafEnabled** | 用于确定是否启用了 WAF 的设置。|
   | **wafMode** | Web 应用程序防火墙的模式。  可用选项有：“预防”  或“检测”  。|
   | **wafRuleSetType** | WAF 的规则集类型。  目前，OWASP 是唯一受支持的选项。 |
   | **wafRuleSetVersion** |规则集版本。 OWASP CRS 2.2.9 和 3.0 目前是支持的选项。 |

1. 检查 **resources** 下的内容，并注意以下属性：

   * **type**。 模板创建的资源的类型。 在这种情况下，类型为 `Microsoft.Network/applicationGateways`，它表示应用程序网关。
   * **name**。 资源的名称。 请注意 `[parameters('applicationGatewayName')]`的使用，这意味着该名称是在部署过程中由用户或参数文件作为输入提供的。
   * **properties**。 资源的属性列表。 此模板在应用程序网关创建过程中，使用虚拟网络与公共 IP 地址。

1. 导航回 [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf)。
1. 单击 **azuredeploy-parameters.json**，然后单击 **RAW**。
1. 将该文件保存到计算机上的本地文件夹中。
1. 打开保存的文件并编辑参数的值。 使用以下值部署本方案中所述的应用程序网关。

     ```json
     {
         "$schema": "https://schema.management.chinacloudapi.cn/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "addressPrefix": {
             "value": "10.0.0.0/16"
             },
             "subnetPrefix": {
             "value": "10.0.0.0/28"
             },
             "applicationGatewaySize": {
             "value": "WAF_Medium"
             },
             "capacity": {
             "value": 2
             },
             "backendIpAddress1": {
             "value": "10.0.1.10"
             },
             "backendIpAddress2": {
             "value": "10.0.1.11"
             },
             "wafEnabled": {
             "value": true
             },
             "wafMode": {
             "value": "Detection"
             },
             "wafRuleSetType": {
             "value": "OWASP"
             },
             "wafRuleSetVersion": {
             "value": "3.0"
             }
         }
     }
     ```

1. 保存文件。 可以使用联机 JSON 验证工具（例如 [JSlint.com](https://www.jslint.com/)）测试 JSON 模板和参数模板。

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>使用 PowerShell 部署 Azure Resource Manager 模板

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果从未使用过 Azure PowerShell，请访问：[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，并按照说明进行操作，以登录到 Azure 并选择订阅。

1. 登录 PowerShell

    ```powershell
    Connect-AzAccount -Environment AzureChinaCloud
    ```

1. 检查该帐户的订阅。

    ```powershell
    Get-AzSubscription
    ```

    系统会提示使用凭据进行身份验证。

1. 选择要使用的 Azure 订阅。

    ```powershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. 如有必要，请使用 **New-AzureResourceGroup** cmdlet 创建资源组。 在下面的示例中，将在“中国北部”位置创建名为 AppgatewayRG 的资源组。

    ```powershell
    New-AzResourceGroup -Name AppgatewayRG -Location "China North"
    ```

1. 运行 **New-AzResourceGroupDeployment** cmdlet，使用在前面下载并修改的模板和参数文件部署新虚拟网络。
    
    ```powershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署 Azure Resource Manager 模板

若要使用 Azure CLI 部署下载的 Azure Resource Manager 模板，请执行以下步骤：

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](/cli/install-azure-cli)，并按照说明进行操作，直到选择 Azure 帐户和订阅。

1. 如有必要，请运行 `az group create` 命令创建一个资源组，如以下代码片段中所示。 请注意命令的输出。 在输出后显示的列表说明了所使用的参数。 有关资源组的详细信息，请访问 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。

    ```azurecli
    az group create --location chinanorth --name appgatewayRG
    ```
    
    **-n（或 --name）** 。 新资源组的名称。 在本方案中为 *appgatewayRG*。
    
    **-l（或 --location）** 。 会创建新资源组的 Azure 区域。 在本方案中为 *chinanorth*。

1. 运行 `az group deployment create` cmdlet，使用上述步骤中下载并修改的模板和参数文件部署新虚拟网络。 在输出后显示的列表说明了所使用的参数。

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>使用“单击部署”来部署 Azure Resource Manager 模板

“单击部署”是另一种使用 Azure 资源管理器模板的方式。 这是将模板与 Azure 门户配合使用的简便方法。

1. 转到 [Azure 门户](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-application-gateway-waf%2fazuredeploy.json)。

1. 在门户上填写部署模板的参数，并单击“确定”  。

    ![参数](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

1. 单击“编辑模板”，检查并更新模板。  单击“保存”  。

    ![参数](./media/application-gateway-create-gateway-arm-template/ibiza2.png)

1. 单击“查看法律条款”，  查看法律条款。 单击“创建”  。 

    ![参数](./media/application-gateway-create-gateway-arm-template/ibiza3.png)

1. 在“自定义部署”边栏选项卡上，单击“创建”  。

    ![参数](./media/application-gateway-create-gateway-arm-template/ibiza4.png)



## <a name="providing-certificate-data-to-resource-manager-templates"></a>向 Resource Manager 模板提供证书数据

如果将 SSL 与模板一起使用，需要提供 base64 字符串格式的证书，而不是上传证书。 若要将 .pfx 或 .cer 转换为 base64 字符串，请运行以下命令之一。 以下命令将证书转换为可提供给模板的 base64 字符串。 预期输出为一个字符串，它可以存储在变量中，并粘贴到模板中。

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请完成以下任一步骤：

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>后续步骤

如果想要配置 SSL 卸载，请访问：[配置应用程序网关以进行 SSL 卸载](application-gateway-ssl.md)。

如果要将应用程序网关配置为与内部负载均衡器配合使用，请访问：[创建具有内部负载均衡器 (ILB) 的应用程序网关](application-gateway-ilb.md)。

如需大体上更详细地了解负载均衡选项，请访问：

* [Azure 负载均衡器](https://www.azure.cn/home/features/load-balancer/)
* [Azure 流量管理器](https://www.azure.cn/home/features/traffic-manager/)

<!-- Update_Description: wording update -->