---
title: 连接提供商和位置：Azure ExpressRoute
description: 本文详细介绍了提供服务的区域以及如何连接到 Azure 区域。 按连接服务提供商排序。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 09/07/2018
ms.author: v-yiso
ms.date: 10/15/2018
ms.openlocfilehash: 557ec7d890b984746fe21f22e664f861b5632238
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627471"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](./expressroute-locations.md)
> * [按位置列出的提供商](./expressroute-locations-providers.md)


本文中的表格提供有关 ExpressRoute 连接提供商、ExpressRoute 地理覆盖范围、通过 ExpressRoute 支持的 Microsoft 云服务以及 ExpressRoute 系统集成商 (SI) 的信息。

## <a name="partners"></a>ExpressRoute 连接服务提供商
所有 Azure 区域和位置都支持 ExpressRoute。 以下地图提供了 Azure 区域和 ExpressRoute 位置的列表。 ExpressRoute 位置是指 Microsoft 与多个服务提供商对等互连的位置。

![位置地图][0]

如果至少与地缘政治区域内的一个 ExpressRoute 位置连接，将有权访问地缘政治区域内所有区域中的 Azure 服务。

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **北美** |美国东部、美国西部、美国东部 2 区、美国中部、美国中南部、美国中北部、加拿大中部、加拿大东部 |亚特兰大、芝加哥、达拉斯、拉斯维加斯、洛杉矶、纽约、西雅图、硅谷、华盛顿特区、蒙特利尔+、魁北克市+、多伦多 |
| **南美洲** |巴西南部 |圣保罗 |
| **欧洲** |北欧、西欧、英国西部、英国南部 |阿姆斯特丹、都柏林、伦敦、纽波特（威尔士）+、巴黎 |
| **亚洲** |东亚、东南亚 |中国香港特别行政区、新加坡 |
| **日本** |日本西部、日本东部 |大坂、东京 |
| **澳大利亚** |澳大利亚东南部、澳大利亚东部 |墨尔本、悉尼 |
| **印度** |印度西部、印度中部、印度南部 |金奈、孟买 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>国家/地区云的区域和地缘政治边界
下表提供了有关国家/地区云的区域和地缘政治边界的信息。

|**地缘政治区域**|**Azure 区域**|**ExpressRoute 位置**|
|---|---|---|---|
|**美国政府云**|US Gov 爱荷华州、US Gov 弗吉尼亚州|芝加哥、达拉斯+、纽约、华盛顿特区|
|**中国**|中国北部、中国东部|北京、上海|
|**德国**|德国中部、德国东部|柏林、法兰克福|

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 需要启用 ExpressRoute 高级外接程序以支持全球连接。 不支持连接到国家/地区云环境。 如果需要，请与连接服务提供商合作。

## <a name="locations"></a>连接服务提供商位置

下表显示按服务提供商列出的位置。 若要按位置查看可用的提供商，请参阅[按位置列出的服务提供商](./expressroute-locations-providers.md#locations)。


### <a name="production-azure"></a>生产 Azure

| **服务提供商**  |**Microsoft Azure** | **Office 365 和 CRM Online** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks]( http://www.aryaka.com/)** | 支持 | 支持 | 阿姆斯特丹、硅谷、新加坡、东京、华盛顿特区 |
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支持 | 支持 | 阿姆斯特丹、芝加哥、达拉斯、伦敦、硅谷、新加坡、悉尼、华盛顿特区 |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 支持 | 支持 | 阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、悉尼、东京、华盛顿特区 |
|**CenturyLink** | 即将支持 | 即将支持| 硅谷 |
|**China Telecom Global** | 支持 | 不支持 | 香港特别行政区 |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | 支持 | 即将支持 | 蒙特利尔+、多伦多 |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  支持 | 支持 | 阿姆斯特丹、都柏林、伦敦、东京 |
| **Comcast** | 支持 | 支持 | 芝加哥、硅谷、华盛顿特区 |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | 支持 | 支持 | 洛杉矶 | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支持 | 支持 | 阿姆斯特丹、亚特兰大、芝加哥、达拉斯、中国香港特别行政区、伦敦、洛杉矶、墨尔本、纽约、大阪、圣保罗、西雅图、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **euNetworks** |  支持 | 支持 | 阿姆斯特丹 |
| **GÉANT** | 即将支持 | 即将支持 | 阿姆斯特丹+ |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  支持 | 支持 | 大坂、东京 |
| **[InterCloud]( https://www.intercloud.com/)** | 支持 | 支持 | 阿姆斯特丹、伦敦、新加坡、华盛顿特区 |
| **Internet 解决方案 - 云连接** | 支持 | 支持 | 阿姆斯特丹、伦敦 |
| **Interxion** | 支持 | 支持 | 阿姆斯特丹、伦敦、巴黎 |
| **Jisc** | 即将支持 | 即将支持 | 伦敦+ | 
| **[Level 3 Communications]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支持 | 支持 | 阿姆斯特丹、芝加哥、达拉斯、拉斯维加斯+、伦敦、西雅图、硅谷、华盛顿特区 |
| **Megaport** | 支持 | 支持 | 达拉斯、中国香港特别行政区、拉斯维加斯+、洛杉矶、墨尔本、纽约、西雅图、新加坡、悉尼、华盛顿特区 |
| **MTN** | 支持 | 支持 | 伦敦 |
| **NEXTDC** | 支持 | 支持 | 墨尔本、悉尼 |
| **NTT Communications** | 支持 | 支持 | 伦敦、大坂、东京 |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | 支持 | 支持 | 阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、华盛顿特区 |
| **PCCW Global Limited** | 支持 | 支持 | 香港特别行政区 |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  支持 | 支持 | 新加坡 |
| **Softbank** | 支持 | 支持 | 大坂、东京 | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 支持 | 支持 | 阿姆斯特丹、金奈、香港特别行政区、伦敦、孟买、硅谷、新加坡、华盛顿特区 |
| **[TeleCity Group]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 支持 | 支持 | 阿姆斯特丹、伦敦 |
| **Telefonica** | 即将支持 | 即将支持 | 圣保罗+ |
| **Telenor** | 支持 | 支持 | 阿姆斯特丹、伦敦 |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支持 | 即将支持 | 墨尔本、悉尼 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | 支持 | 支持 | 阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **Vodafone** | 支持 | 不支持 | 伦敦 | 
| **[Zayo Group]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | 支持 | 支持 | 芝加哥、洛杉矶、纽约、硅谷、多伦多、华盛顿特区 |

 **+** 表示即将推出

### <a name="national-cloud-environments"></a>国家/地区云环境

#### <a name="us-government-cloud"></a>美国政府云

| **服务提供商**  |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支持 | 支持 | 芝加哥、华盛顿特区 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支持 | 支持 | 芝加哥、达拉斯+、纽约、华盛顿特区 |
| **[Level 3 Communications]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支持 | 即将支持 | 芝加哥、纽约+、华盛顿特区 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 支持 | 支持 | 芝加哥、达拉斯+、纽约、华盛顿特区 |

#### <a name="china"></a>中国

| **服务提供商** | **Microsoft Azure** | **Office 365** |   **位置**   |
|----------------------|---------------------|----------------|-------------------|
|  **中国电信**   |      支持      | 不支持  | 北京、上海 |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)。

#### <a name="germany"></a>德国

| **服务提供商**  |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支持 | 不支持 | 柏林+、法兰克福|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 即将支持 | 不支持 | 法兰克福+|
| **e-shelter** | 即将支持 | 不支持 | 柏林+|
| **Interxion** | 支持 | 不支持 | 法兰克福|

## <a name="nonpartners"></a>通过未列出的服务提供商建立连接

如果前面部分中未列出连接服务提供商，仍可以建立连接。

- 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集有关 Exchange 提供商提供服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- 让连接提供商将网络扩展到选择的对等互连位置。
    - 确保连接服务提供商以高可用性方式扩展连接，以防出现单点故障。
- 从 Exchange 连接服务提供商处订购 ExpressRoute 线路以连接到 Microsoft。
    - 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

|**连接服务提供商**|**Exchange**|**位置**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|新加坡|
|**Alaska Communications**|Equinix|西雅图|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|纽约、华盛顿特区|
|**[XO Communications](http://www.xo.com/)**|Equinix|硅谷|

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商

根据网络规模，有时很难启用专用连接来满足需求。 可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。

|**系统集成商**|**所在洲**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| 亚洲、欧洲、美国 |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| 欧洲 |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|美国|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | 亚洲 |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 美国 |
|**[Project Leadership](http://www.projectleadership.net/azure)** | 美国 |

## <a name="next-steps"></a>后续步骤

- 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](./expressroute-faqs.md)。
- 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](./expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"


<!--Update_Description:update meta properties only-->