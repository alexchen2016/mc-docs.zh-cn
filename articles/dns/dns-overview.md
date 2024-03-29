---
title: 什么是 Azure DNS？
description: Microsoft Azure 上的 DNS 托管服务概述。 在 Microsoft Azure 上托管域。
author: WenJason
ms.service: dns
ms.topic: overview
origin.date: 3/21/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: 3660d116a01085a2b8246f88500314b3493893e3
ms.sourcegitcommit: 5b827b325a85e1c52b5819734ac890d2ed6fc273
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503570"
---
# <a name="what-is-azure-dns"></a>什么是 Azure DNS？

Azure DNS 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

不能使用 Azure DNS 来购买域名。 可以通过第三方域名注册机构购买域名，但需支付年费。 然后，可以将域托管在 Azure DNS 中来管理记录。 有关详细信息，请参阅 [向 Azure DNS 委派域](dns-domain-delegation.md)。

Azure DNS 附带了以下功能。

## <a name="reliability-and-performance"></a>可靠性和性能

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 Azure DNS 使用任意广播网络。 每个 DNS 查询由最近的可用 DNS 服务器来应答，为你的域提供快速性能和高可用性。

## <a name="security"></a>安全性

 Azure DNS 基于 Azure 资源管理器，后者提供以下功能：

* [基于角色的访问控制](https://docs.azure.cn/azure-resource-manager/resource-group-overview)：控制谁有权访问针对组织的特定操作。

* [活动日志](https://docs.azure.cn/azure-resource-manager/resource-group-overview)：监视你的组织中的用户对资源进行了怎样的修改，或者在进行故障排除时查找错误。

* [资源锁定](https://docs.azure.cn/azure-resource-manager/resource-group-lock-resources)：锁定订阅、资源组或资源。 锁定可以防止组织中的其他用户意外删除或修改重要资源。

有关详细信息，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。 

## <a name="dnssec"></a>DNSSEC

Azure DNS 当前不支持 DNSSEC。 在大多数情况下，可以通过在应用程序中始终使用 HTTPS/TLS 来减少对 DNSSEC 的需求。 如果 DNSSEC 是 DNS 区域的关键要求，则可以使用第三方 DNS 托管提供者托管这些区域。

## <a name="ease-of-use"></a>易于使用

 Azure DNS 可以管理 Azure 服务的 DNS 记录，还可以为外部资源提供 DNS。 Azure DNS 在 Azure 门户中集成，与其他 Azure 服务使用相同的凭据、支持合同和计费。 

DNS 基于在 Azure 中托管的 DNS 区域数和接收的 DNS 查询数进行计费。 若要深入了解定价，请参阅 [Azure DNS 定价](https://azure.cn/pricing/details/dns/)。

可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务进行集成。

## <a name="next-steps"></a>后续步骤

* 若要了解 DNS 区域和记录，请参阅 [DNS 区域和记录概述](dns-zones-records.md)。

* 若要了解如何在 Azure DNS 中创建区域，请参阅[创建 DNS 区域](./dns-getstarted-create-dnszone-portal.md)。

* 有关 Azure DNS 的常见问题，请参阅 [Azure DNS 常见问题](dns-faq.md)。

