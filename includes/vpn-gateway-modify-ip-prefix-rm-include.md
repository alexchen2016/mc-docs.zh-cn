---
 title: include 文件 description: include 文件 services: vpn-gateway author:WenJason ms.service: vpn-gateway ms.topic: include origin.date:02/14/2019 ms.date:03/04/2019 ms.author: v-jay ms.custom: include file
---
### <a name="noconnection"></a>修改本地网关 IP 地址前缀 - 无网关连接

添加其他地址前缀：

1. 设置 LocalNetworkGateway 的变量。

   ```azurepowershell
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. 修改前缀。

   ```azurepowershell
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

删除地址前缀：

  省去你不再需要的前缀。 在此示例中，我们不再需要前缀 10.101.2.0/24（来自前面的示例），因此需更新本地网关，排除该前缀。

1. 设置 LocalNetworkGateway 的变量。

   ```azurepowershell
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. 使用更新的前缀设置网关。

   ```azurepowershell
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```

### <a name="withconnection"></a>修改本地网关 IP 地址前缀 - 现有网关连接

如果有一个网关连接并且想要添加或删除包含在本地网关中的 IP 地址前缀，则需要按顺序执行以下步骤。 这会导致 VPN 连接中断一段时间。 修改 IP 地址前缀时，不需删除 VPN 网关。 只需删除连接。

1. 删除连接。

   ```azurepowershell
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. 使用修改的地址前缀设置本地网络网关。
   
   设置 LocalNetworkGateway 的变量。

   ```azurepowershell
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   修改前缀。
   
   ```azurepowershell
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. 创建连接。 在此示例中，我们配置 IPsec 连接类型。 重新创建连接时，请使用针对配置指定的连接类型。 有关其他连接类型，请参阅 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 页面。
   
   设置 VirtualNetworkGateway 的变量。

   ```azurepowershell
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   创建连接。 此示例使用在步骤 2 中设置的变量 $local。

   ```azurepowershell
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
    -ResourceGroupName TestRG1 -Location 'China North' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```
