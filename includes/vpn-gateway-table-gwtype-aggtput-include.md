---
 title: include 文件 description: include 文件 services: vpn-gateway author:WenJason ms.service: vpn-gateway ms.topic: include origin.date:12/05/2018 ms.date:12/24/2018 ms.author: v-jay ms.custom: include 文件
---

|**SKU**   | **S2S/VNet 到 VNet<br>隧道** | **P2S<br> SSTP 连接** | **P2S<br> IKEv2 连接** | **聚合<br>吞吐量基准** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**基本** | 最大 10 个    | 最大 128  | 不支持  | 100 Mbps  | 不支持|
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支持 |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支持 |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支持 |


* 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 适用于 VPN 网关的聚合吞吐量基准组合了 S2S 和 P2S。 **如果有大量的 P2S 连接，则可能会对 S2S 连接造成负面影响，因为存在吞吐量限制。** 受 Internet 流量情况和应用程序行为影响，无法保证聚合吞吐量基准。

* 这些连接限制是独立的。 例如，在 VpnGw1 SKU 上可以有 128 个 SSTP 连接，还可以有 250 个 IKEv2 连接。

* 可在 [定价](https://www.azure.cn/pricing/details/vpn-gateway) 页上找到定价信息。

* 可在 [SLA](https://www.azure.cn/support/sla/vpn-gateway/) 页上查看 SLA（服务级别协议）信息。

* 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2 和 VpnGw3 用于 VPN 网关。

* 基本 SKU 被视为旧版 SKU。 基本 SKU 具有某些功能限制。 使用基本 SKU 的网关无法调整为新网关 SKU 中的一种，必须更改为新的 SKU，这就需要删除并新建 VPN 网关。 使用基本 SKU 前，请验证所需功能是否受支持。
