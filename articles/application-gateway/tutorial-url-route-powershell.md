---
title: 基于 URL 对 Web 流量进行路由 - Azure PowerShell
description: 了解如何使用 Azure PowerShell 基于 URL 将 Web 流量路由到特定的可缩放服务器池。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
origin.date: 10/25/2018
ms.date: 06/12/2019
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: 05d1d52b928632cbf38d3af6a730946b87acc2e6
ms.sourcegitcommit: 756a4da01f0af2b26beb17fa398f42cbe7eaf893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67027405"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>使用 Azure PowerShell 基于 URL 对 Web 流量进行路由

可以使用 Azure PowerShell 基于用来访问应用程序的 URL 配置到特定的可缩放服务器池的 Web 流量路由。 在本教程中，将使用[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)创建具有三个后端池的 [Azure 应用程序网关](application-gateway-introduction.md)。 每个后端池都有特定的用途，例如用于公用数据、图像和视频。  将流量路由到不同的池可以确保客户在需要信息时可以获得所需的信息。

若要启用流量路由，需要创建[路由规则](application-gateway-url-route-overview.md)并将其分配给在特定端口上侦听的侦听器以确保 Web 流量到达池中的合适服务器。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置网络
> * 创建侦听器、URL 路径映射和规则
> * 创建可缩放的后端池

![URL 路由示例](./media/tutorial-url-route-powershell/scenario.png)

如果你愿意，可以使用 [Azure CLI](tutorial-url-route-cli.md) 或 [Azure 门户](create-url-route-portal.md)完成本教程中的步骤。

如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 若要查找版本，请运行 `Get-Module -ListAvailable Az`。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount -Environment AzureChinaCloud` 来创建与 Azure 的连接。

因为创建资源需要时间，所以可能需要最多 90 分钟才能完成本教程。

## <a name="create-a-resource-group"></a>创建资源组

需要创建包含应用程序的所有资源的资源组。 

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。  

```azurepowershell
New-AzResourceGroup -Name myResourceGroupAG -Location chinanorth
```

## <a name="create-network-resources"></a>创建网络资源

无论是具有现有虚拟网络还是创建新的虚拟网络，都需要确保它包含一个仅用于应用程序网关的子网。 在本教程中，将为应用程序网关创建一个子网，为规模集创建一个子网。 将创建一个公用 IP 地址以便访问应用程序网关中的资源。

使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网配置 *myAGSubnet* 和 *myBackendSubnet*。 使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) 和子网配置创建名为 *myVNet* 的虚拟网络。 最后使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) 创建名为 *myAGPublicIPAddress* 的公共 IP 地址。 这些资源用于提供与应用程序网关及其关联资源的网络连接。

```azurepowershell
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

本部分将创建用来支持应用程序网关的资源，并最终创建应用程序网关。 创建的资源包括：

- *IP 配置和前端端口* - 将先前创建的子网关联到应用程序网关，并分配一个端口以用于访问它。
- *默认池* - 所有应用程序网关必须至少具有一个后端服务器池。
- *默认侦听器和规则* - 默认侦听器侦听已分配的端口上的流量，默认规则将流量发送到默认池。

### <a name="create-the-ip-configurations-and-frontend-port"></a>创建 IP 配置和前端端口

使用 [New-AzApplicationGatewayIPConfiguration](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayipconfiguration) 将前面创建的 *myAGSubnet* 关联到应用程序网关。 使用 [New-AzApplicationGatewayFrontendIPConfig](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) 将 *myAGPublicIPAddress* 分配给应用程序网关。

```azurepowershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>创建默认池和设置

使用 [New-AzApplicationGatewayBackendAddressPool](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) 为应用程序网关创建名为 *appGatewayBackendPool* 的默认后端池。 使用 [New-AzApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) 配置后端池的设置。

```azurepowershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>创建默认侦听器和规则

应用程序网关需要侦听器才能适当地将流量路由到后端池。 在本教程中，将创建两个侦听器。 创建的第一个基本侦听器侦听根 URL 上的流量。 创建的第二个侦听器侦听特定 URL 上的流量。

使用 [New-AzApplicationGatewayHttpListener](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayhttplistener) 以及前面创建的前端配置和前端端口创建名为 *myDefaultListener* 的默认侦听器。 

侦听器需要使用规则来了解哪个后端池使用传入流量。 使用 [New-AzApplicationGatewayRequestRoutingRule](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) 创建一个名为 *rule1* 的基本规则。

```azurepowershell
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>创建应用程序网关

现在已创建所需的支持资源，请使用 [New-AzApplicationGatewaySku](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaysku) 为名为 *myAppGateway* 的应用程序网关指定参数，然后再使用 [New-AzApplicationGateway](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgateway) 创建它。

```azurepowershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

创建应用程序网关可能需要最多 30 分钟，请等到部署成功完成，然后转到下一部分。 在教程中，此时已有了侦听端口 80 上的流量并将该流量发送到默认的服务器池的一个应用程序网关。

### <a name="add-image-and-video-backend-pools-and-port"></a>添加映像及视频后端池和端口

向应用程序网关 [Add-AzApplicationGatewayBackendAddressPool](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool) 添加名为 *imagesBackendPool* 和 *videoBackendPool* 的后端池。 使用 [Add-AzApplicationGatewayFrontendPort](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayfrontendport) 添加池的前端端口。 使用 [Set-AzApplicationGateway](https://docs.microsoft.com/powershell/module/az.network/set-azapplicationgateway) 提交对应用程序网关所做的更改。

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

更新应用程序网关可能也需要最多 20 分钟才能完成。

### <a name="add-backend-listener"></a>添加后端侦听器

使用 [Add-AzApplicationGatewayHttpListener](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistener) 添加路由流量所需的名为 *backendListener* 的后端侦听器。

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>添加 URL 路径映射

URL 路径映射可确保将发送到应用程序的 URL 路由到特定的后端池。 使用 [New-AzApplicationGatewayPathRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) 和 [Add-AzApplicationGatewayUrlPathMapConfig](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig) 创建名为 *imagePathRule* 和 *videoPathRule* 的 URL 路径映射。

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>添加路由规则

路由规则可将 URL 映射与所创建的侦听器相关联。 使用 [Add-AzApplicationGatewayRequestRoutingRule](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule) 添加名为 *rule2* 的规则。

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>创建虚拟机规模集

在此示例中，将创建三个虚拟机规模集以支持所创建的三个后端池。 创建的规模集分别名为 *myvmss1*、*myvmss2* 和 *myvmss3*。 配置 IP 设置时将规模集分配给后端池。

```azurepowershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location chinanorth `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>安装 IIS

每个规模集都包含两个虚拟机实例，你将在其上安装并使用 IIS 来运行示例页面以测试应用程序网关是否工作。

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 获取应用程序网关的公共 IP 地址。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如，`http://52.168.55.24`、`http://52.168.55.24:8080/images/test.htm` 或 `http://52.168.55.24:8080/video/test.htm`。

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![在应用程序网关中测试基 URL](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

将 URL 更改为 http://&lt;ip-address&gt;:8080/images/test.htm（将 &lt;ip-address&gt; 替换为自己的 IP 地址），应会看到如以下示例所示的内容：

![在应用程序网关中测试映像 URL](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

将 URL 更改为 http://&lt;ip-address&gt;:8080/video/test.htm（请将 &lt;ip-address&gt; 替换为自己的 IP 地址），应会看到如以下示例所示的内容：

![在应用程序网关中测试视频 URL](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、应用程序网关和所有相关资源，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 将其删除。

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 设置网络
> * 创建侦听器、URL 路径映射和规则
> * 创建可缩放的后端池

> [!div class="nextstepaction"]
> [基于 URL 重定向 Web 流量](./tutorial-url-redirect-powershell.md)

<!-- Update_Description: code and links update -->