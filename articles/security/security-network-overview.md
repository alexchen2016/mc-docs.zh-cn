---
title: Azure 网络安全的概念和要求 | Azure
description: 本文提供了关于核心网络安全概念和要求的基本说明，以及 Azure 在每个方面提供的内容的相关信息。
services: security
documentationcenter: na
author: lingliw
manager: digimobile
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 10/29/2018
ms.date: 11/26/2018
ms.author: v-lingwu
ms.openlocfilehash: 8bd8f3dbd496a9ae97565006b862cc9d6f4ec6df
ms.sourcegitcommit: 579d4e19c2069ba5c7d5cb7e9b233744cc90d1f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53219572"
---
# <a name="azure-network-security-overview"></a>Azure 网络安全概述

网络安全可以定义为通过对网络流量应用控制来保护资源遭受未经授权的访问或攻击的过程。 目标是确保仅允许合法流量。 Azure 包括可靠的网络基础结构以支持应用程序和服务连接需求。 Azure 中的资源之间、本地资源与 Azure 托管的资源之间，以及 Internet 与 Azure 之间都可能存在网络连接。

本文介绍 Azure 在网络安全方面提供的某些选项。 具体内容：

* Azure 网络
* 网络访问控制
* Azure 防火墙
* 安全远程访问和跨界连接
* 可用性
* 名称解析
* 外围网络 (DMZ) 体系结构
* Azure DDoS 防护
* Azure Front Door
* 流量管理器
* 监视和威胁检测

## <a name="azure-networking"></a>Azure 网络

Azure 要求将虚拟机连接到 Azure 虚拟网络。 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个虚拟网络与其他所有虚拟网络相互隔离。 这可帮助确保其他 Azure 客户无法访问部署中的流量。

## <a name="network-access-control"></a>网络访问控制

网络访问控制是限制虚拟网络内特定设备或子网之间的连接的行为。 网络访问控制的目的是将对虚拟机和服务的访问权限限制为仅授予已批准的用户和设备。 访问控制基于虚拟机或服务之间的允许或拒绝连接的决策。

Azure 支持多种类型的网络访问控制，例如：

* 网络层控制
* 路由控制和强制隧道
* 虚拟网络安全设备

### <a name="network-layer-control"></a>网络层控制
任何安全部署都需要某种程度的网络访问控制。 网络访问控制的目标是确保虚拟机以及在这些虚拟机上运行的网络服务只能与它们需要通信的其他网络设备通信，阻止所有其他连接企图。

如果需要基本的网络级别访问控制（基于 IP 地址和 TCP 或 UDP 协议），则可以使用网络安全组。 网络安全组 (NSG) 是基本的有状态数据包筛选防火墙，它使你能够基于[五元组](https://www.techopedia.com/definition/28190/5-tuple)控制访问。 NSG 不提供应用程序层检查或经过身份验证的访问控制。

了解详细信息：

- [网络安全组](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>路由控制和强制隧道
能够控制虚拟网络上的路由行为至关重要。 如果路由配置不正确，虚拟机上托管的应用程序和服务可能会连接到未授权的设备，其中包括潜在攻击者所拥有或操作的系统。

Azure 网络支持在虚拟网络上为流量自定义路由行为。 由此可更改 Azure 虚拟网络中的默认路由表条目。 通过控制路由行为，可帮助你确保特定设备或设备组中的所有流量通过特定位置进入或离开虚拟网络。

例如，虚拟网络上可能有虚拟网络安全设备。 想要确保与虚拟网络之间的所有流量都通过该虚拟安全设备。 可以通过在 Azure 中配置[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md) (UDR) 实现此操作。

[强制隧道](https://www.petri.com/azure-forced-tunneling)是一种机制，可用于确保不允许服务启动与 Internet 上设备的连接。 请注意，这与接受并响应传入连接不同。 前端 Web 服务器需要响应来自 Internet 主机的请求，因此允许源自 Internet 的流量传入到这些 Web 服务器，并允许 Web 服务器作出响应。

不想要允许的是前端 Web 服务器发起出站请求。 此类请求可能带来安全风险，因为这些连接可用于下载恶意软件。 即使想要这些前端服务器启动对 Internet 的出站请求，你可能想要强制它们通过本地 Web 代理服务器。 由此可利用 URL 筛选和日志记录。

可以使用强制隧道来避免此问题。 启用强制隧道后，会强制与 Internet 的所有连接通过本地网关。 可以利用 UDR 配置强制隧道。

了解详细信息：

* [什么是用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>虚拟网络安全设备

当 NSG、UDR 和强制隧道在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的网络层和传输层提供安全级别时，你可能也想要启用级别高于网络的安全性。

例如，安全要求可能包括：

* 必须经过身份验证和授权才允许访问应用程序
* 入侵检测和入侵响应
* 高级别协议的应用程序层检查
* URL 筛选
* 网络级别防病毒和反恶意软件
* 防 Bot 保护
* 应用程序访问控制
* 其他 DDoS 防护（除了 Azure 结构自身提供的 DDoS 防护以外）

可以使用 Azure 合作伙伴解决方案来访问这些增强的网络安全功能。 通过访问 [Azure 市场](https://market.azure.cn/zh-cn/marketplace/)并搜索“安全”和“网络安全”，可以找到最新的 Azure 合作伙伴网络安全解决方案。

## <a name="azure-firewall"></a>Azure 防火墙

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 包括的一些功能为：

* 高可用性
* 云可伸缩性
* 应用程序 FQDN 筛选规则
* 网络流量筛选规则

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>安全远程访问和跨界连接

安装、配置和管理 Azure 资源需要远程完成。 此外，你可能想要部署在本地和 Azure 公有云中具有组件的[混合 IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) 解决方案。 这些方案需要安全远程访问权限。

Azure 网络支持以下安全远程访问方案：

* 将单独的工作站连接到虚拟网络
* 通过 VPN 将本地网络连接到虚拟网络
* 通过专用的 WAN 链接将本地网络连接到虚拟网络
* 将虚拟网络相互连接

### <a name="connect-individual-workstations-to-a-virtual-network"></a>将单独的工作站连接到虚拟网络

你可能想要让各个开发者或操作人员在 Azure 中管理虚拟机和服务。 例如，假设需要访问虚拟网络上的虚拟机。 但你的安全策略不允许 RDP 或 SSH 远程访问单独的虚拟机。 在这种情况下，可以使用[点到站点 VPN](../vpn-gateway/point-to-site-about.md) 连接。

点到站点 VPN 连接允许你在用户和虚拟网络之间设置专用的安全连接。 建立 VPN 连接后，用户可通过 VPN 链接将 RDP 或 SSH 连接到虚拟网络上的任何虚拟机。 （假设用户可以进行身份验证并获得授权。）点到站点 VPN 支持以下项：

* 安全套接字隧道协议 (SSTP)，这是一种基于 SSL 的专属协议。 由于大多数防火墙都会打开 SSL 所用的 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。

* [OpenVPN](https://www.azure.cn/updates/openvpn-support-for-azure-vpn-gateways/)

了解详细信息：

* [使用 PowerShell 配置与虚拟网络的点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>通过 VPN 将本地网络连接到虚拟网络

你可能想要将整个企业网络或其中的某些部分连接到虚拟网络。 这是常见的混合 IT 方案，通过该方案组织可以[将其本地数据中心扩展到 Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)。 在许多情况下，组织在 Azure 和本地中各托管部分服务。 例如，当解决方案包括 Azure 中的前端 Web 服务器和本地后端数据库时，他们可能会执行此操作。 这些类型的“跨界”连接还使得位于 Azure 的资源的管理更加安全，并且能够启用方案，如将 Active Directory 域控制器扩展到 Azure 中。

实现此目的的方法之一是使用 [站点到站点 VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)。 站点到站点 VPN 和点到站点 VPN 的区别在于后者将单个设备连接到虚拟网络。 站点到站点 VPN 将整个网络（如本地网络）连接到虚拟网络。 连接到 Azure 虚拟网络的站点到站点 VPN 使用高度安全的 IPsec 隧道模式 VPN 协议。

了解详细信息：

* [使用 Azure 门户创建具有站点到站点 VPN 连接的资源管理器 VNet](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [规划和设计 VPN 网关](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>通过专用的 WAN 链接将本地网络连接到虚拟网络

点到站点和站点到站点 VPN 连接可以有效地启用跨界连接。 但是，某些组织认为它们存在以下缺点：

* VPN 连接通过 Internet 移动数据。 这会导致这些连接存在通过公用网络移动数据所涉及的潜在安全问题。 此外，不能保证 Internet 连接的可靠性和可用性。
* 到虚拟网络的 VPN 连接可能没有用于某些应用程序和目的带宽，因为它们达到的最高极限约为 200 Mbps。

需要最高安全性和可用性级别进行其跨界连接的组织通常使用专用 WAN 链路连接到远程网站。 凭借 Azure，可使用专用的 WAN 链接将本地网络连接到虚拟网络。 Azure ExpressRoute、Express Route Direct 和 Express Route Global Reach 实现了此功能。

了解详细信息：

* [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>将虚拟网络相互连接

可以将多个虚拟网络用于部署。 这样做的原因可能有很多。 你可能想要简化管理或提高安全性。 无论将资源放在不同的虚拟网络上的动机是什么，可能有时你都会想要将一个网络上的资源与另一个网络相连接。

一个选择是通过 Internet 以“环回”方式将一个虚拟网络上的服务连接到另一个虚拟网络上的服务。 该连接将在一个虚拟网络上开始，通过 Internet，再回到目标虚拟网络。 此选项会导致连接存在任何基于 Internet 的通信所固有的安全问题。

创建两个虚拟网络之间相互连接的站点到站点 VPN 可能是最佳选择。 此方法与上述的跨界站点到站点 VPN 连接使用相同的 [IPSec 隧道模式](https://technet.microsoft.com/library/cc786385.aspx)协议。

此方法的优点是通过 Azure 网络结构建立 VPN 连接，而不是通过 Internet 进行连接。 与通过 Internet 连接的站点到站点 VPN 相比，这提供了额外的安全层。

了解详细信息：

* [使用 Azure Resource Manager 和 PowerShell 配置 VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>可用性

可用性是任何安全程序的重要组件。 如果用户和系统无法通过网络访问需要访问的内容，则可以认为服务已遭入侵。 Azure 的网络技术支持以下高可用性机制：

* 基于 HTTP 的负载均衡
* 网络级别负载均衡
* 全局负载均衡

负载均衡是专为在多个设备之间均匀分布连接而设计的机制。 负载均衡的目标如下：

* 提高可用性。 在跨多个设备对连接进行负载均衡时，一个或多个设备可能变得不可用，但不影响服务。 在剩余的联机设备上运行的服务可继续提供服务中的内容。
* 提高性能。 在跨多个设备对连接进行负载均衡时，单个设备不必负责所有处理。 提供内容的处理和内存需求分散在多个设备之间。

### <a name="http-based-load-balancing"></a>基于 HTTP 的负载均衡

运行基于 Web 的服务的组织通常希望在这些 Web 服务前面具有基于 HTTP 的负载均衡器。 这可帮助确保足够级别的性能和高可用性。 基于网络的传统负载均衡器依赖于网络和传输层协议。 另一方面，基于 HTTP 的负载均衡器根据 HTTP 协议的特性做出决策。

Azure 应用程序网关为基于 Web 的服务提供了基于 HTTP 的负载均衡。 应用程序网关支持：

* 基于 Cookie 的会话关联。 此功能可确保建立到负载均衡器后面的某个服务器的连接在客户端和服务器之间保持不变。 此操作确保了事务的稳定性。
* SSL 卸载。 当客户端与负载均衡器连接时，会话使用 HTTPS (SSL) 协议进行加密。 但是，为了提高性能，可以使用 HTTP（未加密）协议在负载均衡器和该负载均衡器后面的 Web 服务器之间进行连接。 这称为“SSL 卸载”，因为负载均衡器后面的 Web 服务器不会遇到涉及加密的处理器开销。 因此 Web 服务器可更快地为请求提供服务。
* 基于 URL 的内容路由。 此功能可使负载均衡器决定在哪里转接基于目标 URL 的连接。 它提供的弹性大于基于 IP 地址做出负载均衡决策的解决方案。

### <a name="network-level-load-balancing"></a>网络级别负载均衡

与基于 HTTP 的负载均衡相比，网络级别负载均衡基于 IP 地址和端口（TCP 或 UDP）号做出决策。
使用 Azure 负载均衡器，可以在 Azure 中获得网络级别负载均衡的优点。 负载均衡器的一些主要特征包括：

* 基于 IP 地址和端口号的网络级别负载均衡。
* 支持任何应用层协议。
* 对 Azure 虚拟机和云服务角色实例进行负载均衡。
* 可用于面向 Internet（外部负载均衡）和面向非 Internet（内部负载均衡）的应用程序和虚拟机。
* 终结点监视，可用于确定负载均衡器后面的任何服务是否已变得不可用。

了解详细信息：

* [多个虚拟机或服务之间的面向 Internet 的负载均衡器](../load-balancer/load-balancer-internet-overview.md)
* [内部负载均衡器概述](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>全局负载均衡

某些组织可能想要最高级别的可用性。 实现此目标的方法之一是将应用程序托管到多区域分布的数据中心。 在分布于世界各地的数据中心托管应用程序时，即使整个地缘政治区域变得不可用，应用程序也可以启动并运行。

此负载平衡策略也可暂停性能优势。 可直接向距离提出请求的设备最近的数据中心请求服务。

在 Azure 中，使用 Azure 流量管理器可以获得多区域负载均衡的优点。

了解详细信息：

* [什么是流量管理器？](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>名称解析

名称解析是对 Azure 中托管的所有服务而言至关重要的功能。 从安全角度看，入侵名称解析功能可能会导致攻击者将你站点的请求重定向到攻击者的站点。 安全名称解析是所有云托管服务的要求。

需要解决两种类型的名称解析：

* 内部名称解析。 虚拟网络和/或本地网络上的服务使用此名称解析。 用于内部名称解析的名称无法通过 Internet 访问。 为了获取最高安全性，外部用户不能访问内部名称解析方案，这一点非常重要。
* 外部名称解析。 本地网络和虚拟网络之外的人员和设备使用此名称解析。 这些是对 Internet 可见且用于将连接定向到基于云的服务的名称。

对于内部名称解析，可以使用两个选项：

* 虚拟网络 DNS 服务器。 创建新的虚拟网络时，会为你创建 DNS 服务器。 此 DNS 服务器可以解析位于该虚拟网络上的计算机的名称。 此 DNS 服务器是不可配置的，而且由 Azure 结构管理器进行管理，从而帮助对名称解析解决方案进行安全保护。
* 自带 DNS 服务器。 可选择将自己选择的 DNS 服务器放置在虚拟网络上。 此 DNS 服务器可以是 Active Directory 集成的 DNS 服务器或由 Azure 合作伙伴提供的专用 DNS 服务器解决方案，两者均可从 Azure 市场中获得。

了解详细信息：

- [虚拟网络概述](../virtual-network/virtual-networks-overview.md)
- [管理虚拟网络 (VNet) 使用的 DNS 服务器](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

对于外部名称解析，有两个选项：

* 在本地托管自己的外部 DNS 服务器。
* 通过服务提供程序托管自己的外部 DNS 服务器。

许多大型组织在本地托管自己的 DNS 服务器。 可以这样做的原因是它们具有相应的网络专业技术，并且在多个区域运营。

在大多数情况下，最好在服务提供商那里托管 DNS 名称解析服务。 这些服务提供商具有网络专业技术并在多个区域运营，可确保名称解析服务具有极高的可用性。 可用性是 DNS 服务所必需的，因为如果名称解析服务失败，则任何人都将无法访问面向 Internet 的服务。

Azure 以 Azure DNS 的形式提供一个高可用性且高性能的外部 DNS 解决方案。 此外部名称解析解决方案利用多区域 Azure DNS 基础结构。 由此可使用与其他 Azure 服务相同的凭据、API、工具和计费在 Azure 中托管域。 由于属于 Azure 的一部分，它还会继承平台内置的强大安全控制。



## <a name="dmz-architecture"></a>外围网络体系结构
许多企业组织使用 DMZ 对其网络进行分段，以创建 Internet 及其服务之间的缓冲区域。 网络的外围网络部分被视为低安全性区域，不应在该网段中放置高价值资产。 通常会看到网络安全设备在外围网络段上有一个网络接口，另有一个网络接口连接到包含接受 Internet 入站连接的虚拟机和服务的网络。

外围网络设计和外围网络部署决策有许多变数，如果决定使用外围网络，要使用的外围网络类型应该根据网络安全要求来确定。
