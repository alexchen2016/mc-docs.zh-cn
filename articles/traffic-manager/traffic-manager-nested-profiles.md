---
title: Azure 中的嵌套式流量管理器配置文件
titlesuffix: Azure Traffic Manager
description: 本文介绍了 Azure 流量管理器的“嵌套式配置文件”功能
services: traffic-manager
documentationcenter: ''
author: rockboyfor
manager: digimobile
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 10/22/2018
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: cfb1aa0e643b1787c8d9d2bf2c48ab4e2cdcd55d
ms.sourcegitcommit: e32c8da268002b94c500131bb361fd6afc85ce9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56306721"
---
<!-- Region Map  West US China North, West Europe China North 2, and East Asia China East-->
# <a name="nested-traffic-manager-profiles"></a>嵌套式流量管理器配置文件

流量管理器包含一系列流量路由方法，可用于控制流量管理器如何选择具体的终结点来从每个最终用户处接收流量。 有关详细信息，请参阅[流量管理器流量路由方法](traffic-manager-routing-methods.md)。

每个流量管理器配置文件都会指定一个流量路由方法。 但在某些情况下，所需的流量路由方法比单个流量管理器配置文件所提供的方法更复杂。 可以嵌套流量管理器配置文件以结合一种以上的流量路由方法的优势。 使用嵌套式配置文件可以重写默认的流量管理器行为，支持更大、更复杂的应用程序部署。

以下示例演示如何在不同的方案中使用嵌套式流量管理器配置文件。

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>示例 1：组合使用“性能”和“加权”流量路由

假设在以下 Azure 区域部署了一个应用程序：中国北部、中国北部 2 区和中国东部。 使用流量管理器的“性能”流量路由方法将流量分发到最靠近用户的区域。

<!-- Region Map  West US China North, West Europe China North 2, and East Asia China East-->

![单个流量管理器配置文件][4]

现在，假设用户要针对服务的某项更新进行测试，再推广该更新。 你想要使用“加权”流量路由方法，以便将少部分流量定向到测试部署。 你在华北设置了测试部署和现有的生产部署。

无法在单个配置文件中结合使用“加权”和“性能”流量路由方法。 若要支持此方案，可以使用位于中国北部 2 区的两个终结点和“加权”流量路由方法创建流量管理器配置文件。 接下来，将此“子”配置文件作为终结点添加到“父”配置文件。 父配置文件仍使用“性能”流量路由方法，并且仍包含终结点形式的其他全局部署。

<!-- Notice: Europe West against China North 2-->

下图演示了此示例：

![嵌套式流量管理器配置文件][2]

在此配置中，通过父配置文件定向的流量可跨区域正常分发。 在中国北部 2 区，嵌套式配置文件根据分配的权重将流量分发到生产和测试终结点。

<!-- Notice: Europe West against China North 2-->

当父配置文件使用“性能”流量路由方法时，必须为每个终结点分配一个位置。 可在配置终结点时分配该位置。 请选择离部署最近的 Azure 区域。 Azure 区域是 Internet 延迟表支持的位置值。 有关详细信息，请参阅[流量管理器“性能”流量路由方法](traffic-manager-routing-methods.md#performance)。

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>示例 2：嵌套式配置文件中的终结点监视

流量管理器会主动监视每个服务终结点的运行状况。 如果终结点不正常，流量管理器会将用户定向到替代终结点，保持服务的可用性。 这种终结点监视和故障转移行为适用于所有流量路由方法。 有关详细信息，请参阅[流量管理器终结点监视](traffic-manager-monitoring.md)。 终结点监视的工作方式不同于嵌套式配置文件。 对于嵌套式配置文件，父配置文件不会针对子配置文件直接执行运行状况检查。 子配置文件的终结点运行状况用于计算子配置文件的总体运行状况。 此运行状况信息会在嵌套式配置文件层次结构中向上传播。 父配置文件使用聚合运行状况信息确定是否将流量定向到子配置文件。 有关嵌套式配置文件运行状况监视的完整详细信息，请参阅[常见问题解答](traffic-manager-FAQs.md#traffic-manager-nested-profiles)。

回到前面的示例，假设华北的生产部署发生故障。 默认情况下，“子”配置文件会将所有流量定向到测试部署。 如果测试部署也发生故障，父配置文件将确定子配置文件是否不应接收流量，因为所有子终结点都不正常。 然后，父配置文件将流量分发到其他区域。

![嵌套式配置文件故障转移（默认行为）][3]

可能对这种流程感到满意。 或者，可能担心中国北部 2 区的所有流量（而不是有限数量的流量）现在都会转到测试部署。 不管测试部署的运行状况如何，都希望中国东部的生产部署发生故障时，能够故障转移到其他区域。 要启用这种故障转移，可以在将子配置文件配置为终结点时，在父配置文件中指定“MinChildEndpoints”参数。 该参数确定子配置文件中可用终结点的最小数目。 默认值为“1”。 在本方案中，可将 MinChildEndpoints 值设置为 2。 如果低于此阈值，父配置文件会将整个子配置文件视为不可用，并将流量定向到其他终结点。

<!-- Notice: Europe West against China North 2-->

下图演示了此配置：

![嵌套式配置文件故障转移，“MinChildEndpoints”设置为 2][4]

> [!NOTE]
> “优先级”流量路由方法将所有流量分发到单个终结点。 因此，为子配置文件设置除“1”以外的 MinChildEndpoints 值几乎没有作用。

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>示例 3：“性能”流量路由中的优先故障转移区域

“性能”流量路由方法的默认行为是，当终结点位于不同地理位置时，将依据最低网络延迟将最终用户路由到“最近”的终结点。

但是，假设希望将中国北部 2 区的流量故障转移到中国北部，仅当这两个终结点都不可用时才将流量定向到其他区域。 可以使用包含“优先级”流量路由方法的子配置文件创建此解决方案。

<!-- Region Map  West US China North, West Europe China North 2-->

![首选故障转移的“性能”流量路由][6]

由于中国北部 2 区终结点的优先级高于中国北部终结点，因此当两个终结点都处于联机状态时，所有流量将发送到中国北部 2 区终结点。 如果中国北部 2 区发生故障，则其流量会定向到中国北部。 如果使用嵌套式配置文件，仅当中国北部和中国北部 2 区都发生故障时，流量才定向到中国东部。

可以针对所有区域重复此模式。 将父配置文件中的所有三个终结点替换为三个子配置文件，每个子配置文件提供故障转移优先顺序。

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>示例 4：控制同一区域中多个终结点之间的“性能”流量路由

假设在配置文件中使用“性能”流量路由方法时，该配置文件在特定区域有多个终结点。 默认情况下，定向到该区域的流量会平均分布到该区域的所有可用终结点。

![“性能”流量路由区域内流量分布（默认行为）][7]

无需在中国北部 2 区添加多个终结点，这些终结点会包含在单独的子配置文件中。 子配置文件将作为中国北部 2 区的唯一终结点添加到父配置文件。 子配置文件中的设置可以通过在中国北部 2 区启用基于优先级或加权的流量路由，控制该区域的流量分布。

<!-- Region Map West Europe China North 2-->
![“性能”流量路由，自定义区域内流量分布][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>示例 5：基于终结点的监视设置

假设希望使用流量管理器来顺利地将流量从旧的本地网站迁移到基于云的新版网站（托管在 Azure 中）。 对于旧站点，想要使用主页 URI 监视站点运行状况。 但对于基于云的新版站点，你要实现一个包含附加检查的自定义监视页面（路径为“/monitor.aspx”）。

![流量管理器终结点监视（默认行为）][9]

流量管理器配置文件中的监视设置应用到单个配置文件中的所有终结点。 使用嵌套式配置文件时，可为每个站点使用一个不同的子配置文件来定义不同的监视设置。

![按终结点进行设置的流量管理器终结点监视][10]

## <a name="next-steps"></a>后续步骤

深入了解[流量管理器配置文件](traffic-manager-overview.md)

了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

<!--Update_Description: update meta properties -->
