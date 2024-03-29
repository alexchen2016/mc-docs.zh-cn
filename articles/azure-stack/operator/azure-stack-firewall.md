---
title: Azure Stack 集成系统的 Azure Stack 防火墙计划 | Microsoft Docs
description: 介绍了多节点 Azure Stack Azure 连接部署的 Azure Stack 防火墙注意事项。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9dc01a5c43a99c7d8369c10790c919fd07227e00
ms.sourcegitcommit: 05aa4e4870839a3145c1a3835b88cf5279ea9b32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64529728"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack 防火墙集成
建议使用防火墙设备来帮助保护 Azure Stack。 防火墙有助于防止分布式拒绝服务 (DDOS) 攻击之类的攻击，以及执行入侵检测和内容检查。 但是，它们也可能成为 Azure 存储服务（例如 Blob、表和队列）的吞吐量瓶颈。

 如果使用断开连接部署模式，则必须发布 AD FS 终结点。 有关详细信息，请参阅[数据中心集成标识一文](azure-stack-integrate-identity.md)。

Azure 资源管理器（管理员）、管理员门户和 Key Vault（管理员）终结点不一定需要外部发布。 例如，作为服务提供商，你可以只允许从网络内管理 Azure Stack，不允许从 Internet 进行管理，这样就可以限制攻击面。

对于企业组织，外部网络可能是现有的公司网络。 在这种情况下，必须发布可以从公司网络操作 Azure Stack 的终结点。

### <a name="network-address-translation"></a>网络地址转换
网络地址转换 (NAT) 是建议采用的方法，它允许部署虚拟机 (DVM) 在部署期间访问外部资源和 Internet，以及在注册和故障排除期间访问紧急恢复控制台 (ERCS) VM 或特权终结点 (PEP)。

还可以使用 NAT 作为外部网络上的公共 IP 地址或公共 VIP 的替代方法。 但是，不建议这样做，因为它限制了租户用户体验并增加了复杂性。 一个选项是一对一 NAT，仍然要求池中的每个用户 IP 有一个公共 IP。 另一个选项是多对一 NAT，需要针对用户可能使用的所有端口按用户 VIP 采用 NAT 规则。

下面是对公共 VIP 使用 NAT 的一些缺点：
- NAT 增加了管理防火墙规则时的开销，因为用户在软件定义的网络 (SDN) 堆栈中控制其自己的终结点和其自己的发布规则。 用户必须联系 Azure Stack 操作员才能发布其 VIP 以及更新端口列表。
- 虽然使用 NAT 会限制用户体验，但它使得操作员能够完全控制发布请求。
- 对于采用 Azure 的混合云方案，请注意 Azure 不支持使用 NAT 设置到终结点的 VPN 隧道。

### <a name="ssl-decryption"></a>SSL 解密
当前建议禁用对所有 Azure Stack 流量的 SSL 解密。 如果将来的更新中支持此功能，那时将会提供有关如何为 Azure Stack 启用 SSL 解密的指南。

## <a name="edge-firewall-scenario"></a>边缘防火墙方案
在边缘部署中，Azure Stack 直接部署在边缘路由器或防火墙后面。 在这些方案中，支持将防火墙放置在边界上方（例如方案 1，在这种情况下它支持主动-主动和主动-被动防火墙配置）或让防火墙充当边界设备（例如方案 2，在这种情况下它仅支持依赖于等成本多路径 (ECMP) 的主动-主动防火墙配置，并使用 BGP 或静态路由进行故障转移）。

在部署时会为外部网络中的公共 VIP 池指定公共的可路由 IP 地址。 在边缘方案中，出于安全考虑，建议不要在任何其他网络上使用公共的可路由 IP。 与在 Azure 之类的公有云中一样，此方案使得用户能够获得完全的自控云体验。  

![Azure Stack 边缘防火墙示例](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企业 Intranet 或外围网络防火墙方案
在企业 Intranet 或外围部署中，Azure Stack 部署在多区域防火墙上或者部署在边缘防火墙与内部的公司网络防火墙之间。 然后，其流量将分布在安全的外围网络（或 DMZ）与不安全的区域之间，如下所述：

- **安全区域**：这是使用内部或公司可路由 IP 地址的内部网络。 安全网络可以拆分，可以通过防火墙上的 NAT 进行 Internet 出站访问，并且通常可以通过内部网络从你的数据中心内的任何位置进行访问。 除了外部网络的公共 VIP 池之外，所有 Azure Stack 网络都应当位于安全区域中。
- **外围区域**。 通常在外围网络中部署面向外部或 Internet 的应用程序，例如 Web 服务器。 通常由防火墙对其进行监视，以避免诸如 DDoS 和入侵（黑客进攻）之类的攻击，同时允许来自 Internet 的指定入站流量。 只有 Azure Stack 的外部网络公共 VIP 池应当位于 DMZ 区域中。
- **不安全区域**。 这是指外部网络，即 Internet。 建议**不要**将 Azure Stack 部署在不安全区域中。

![Azure Stack 外围网络示例](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>了解详细信息
详细了解 [Azure Stack 终结点使用的端口和协议](azure-stack-integrate-endpoints.md)。

## <a name="next-steps"></a>后续步骤
[Azure Stack PKI 要求](azure-stack-pki-certs.md)

