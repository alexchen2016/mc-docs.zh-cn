---
title: 了解 Azure 帐单 | Microsoft Docs
description: 了解如何阅读并理解 Azure 订阅的使用情况和计费
services: ''
documentationcenter: ''
author: genlin
manager: ruchic
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: v-junlch
ms.openlocfilehash: ab107b0b4bc11dee492fe0c59dfd1b11c38c7d28
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643955"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帐单
若要了解 Azure 帐单，请查看包含费用摘要的发票和单独的每日详细使用情况文件。 本文对发票和每日使用情况文件中显示的大部分条款进行说明。 如果使用的是免费试用订阅，则可以获得每日使用情况信息，但没有发票。

Microsoft Azure 订阅的费用因资费套餐而异。 某些资费套餐（如 Visual Studio Enterprise (MPN) 订户）包含可以根据需要用于 Azure 服务的每月信用额度。

在上一计费周期结束时，最多 24 小时的使用情况可能显示在当前帐单中。 另外，对于国际客户，帐单上列出的费用仅用于估算目的。 不同的银行根据不同的兑换率计算费用。

## <a name="pdf"></a>了解发票 (.pdf)
发票提供费用的摘要。 可以从 [Azure 门户](https://portal.azure.cn)下载可移植文档格式 (.pdf) 的发票。 

下列部分列出了可在发票上看到的大多数条款以及每个条款的说明。

### <a name="understand-the-invoice-summary"></a>了解发票汇总
帐单的“发票汇总”部分汇总了上次帐单之后的交易以及当前的使用费。

![发票汇总](./media/billing-understand-your-bill/InvoiceSummary.png)

帐单的前期结余、付款和未付余额部分汇总了上次帐单之后的交易。

| 术语 | 说明 |
| --- | --- |
| 前期结余 |上次帐单中结余的总金额 |
| 付款 |应用到上次帐单的总付款金额 |
| 未付余额（从上一计费周期） |上次帐单之后应用到帐户的任何帐单调整（信用额度或结余） |

### <a name="understand-the-current-charges"></a>了解当前费用
帐单的“当前费用”部分显示有关每月费用的详细信息。 

| 术语 | 说明 |
| --- | --- |
| 使用费 |使用费是订阅的总月度费用。 我们会根据你过去一个月的使用量向你收取费用。 |
| 折扣 |应用到当前帐单的服务折扣。 |
| 调整 |应用于当前帐单的其他信用额度或未清费用。 例如，如果你享有 Visual Studio Enterprise with MSDN 优惠，就会看到每月信用额度。 如果取消订阅，你会看到超出通过订阅套餐获得的每月信用额度的所有每月使用费用。 费用从当前计费周期的开始计算，直到订阅取消日期。 |

## <a name="csv"></a>了解详细的使用费 (.csv)
使用情况文件显示在当前计费周期内每个资源使用了多少。 它以逗号分隔值 (.csv) 文件格式提供，你可以在电子表格应用程序中打开它。 如果看到两个版本可用，请下载版本 2。 这是最新的文件格式。

使用费是订阅的**每月**总费用减去任何信用额度或折扣。 我们会根据你过去一个月的使用量向你收取费用。  

以下部分介绍版本 2 的详细使用情况文件中显示的大部分条款。

### <a name="statement"></a>语句 
文件的顶部显示了你在上个月的计费周期期间使用的服务。 下表列出了此部份中显示的条款和说明。

| 术语 | 说明 |
| --- | --- |
|计费周期 |使用资源或服务时的计费周期。 |
|测定仪类别 |列出该次使用所属的最上层服务。 |
|测定仪子类别 |定义 Azure 服务的类型，该类型可能会影响费率。 |
|测定仪名称 |列出耗用资源的度量单位。 |
|测定仪区域 |指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
|SKU |指明每个 Azure 资源的唯一系统标识符。 |
|计价单位 |指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
|已耗用数量 |计费周期期间使用的资源量。 |
|附送数量 |当前计费周期免费提供的资源量。 |
|超额数量 |显示已耗用数量和已包含数量之间的差异。 将根据此数量对你计费。 对于不附送任何数量的即用即付产品/服务，此总数与已耗用数量相同。 |
|在承诺额范围内 |显示从与你的 6 或 12 个月产品/服务关联的承诺金额中减去的资源费用。 按时间顺序减去资源费用。 |
|货币 |当前计费期间内使用的货币。 |
|超额 |显示超过与你的 6 或 12 个月产品/服务关联的承诺金额的资源费用。 |
|承诺费率 |显示基于与你的 6 或 12 个月产品/服务关联的总承诺金额的承诺费率。 |
|费率 |为每个可付费单位支付的费率。 |
|值 |显示将“超额数量”列与“费率”列相乘的结果。 如果已耗用数量未超过已包含数量，则此列中没有费用。 |

### <a name="daily-usage"></a>每日使用情况 

该文件的“每日使用情况”部分显示影响计费费率的使用详细信息。 下表列出了此部份中显示的条款和说明。 

| 术语| 说明 |
| --- | --- |
|使用日期 |使用资源时的日期。 |
|测定仪类别 |列出该次使用所属的最上层服务。 |
|测定仪 ID |用于计算用量费用的费用计量标识符。 |
|测定仪子类别 |定义 Azure 服务类型，该类型可能会影响费率。 |
|测定仪名称 |列出耗用资源的度量单位。 |
|测定仪区域|指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
|计价单位 |指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
|已耗用数量 |当日已耗用的资源量。 |
|资源位置 |指明资源正在其中运行的数据中心。 |
|已耗用的服务 |你使用的 Azure 平台服务。 |
|资源组 |部署的资源正在其中运行的资源组。 有关详细信息，请参阅 [Azure Resource Manager 概述](azure-resource-manager/resource-group-overview.md)。 |
|实例 ID |资源的标识符。 此标识符包含在资源创建时为其指定的名称。 它是资源的名称或完全限定的资源 ID。 有关详细信息，请参阅 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources)。 |
|标记 |分配给资源的标记。 使用标记对计费记录进行分组。 例如，可以使用标记按使用资源的部门分配费用。 支持发出标记的服务包括虚拟机、存储和使用 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) 预配的网络服务。 有关详细信息，请参阅[使用标记来组织 Azure 资源](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。 |
|其他信息 |服务特定的元数据。 例如，虚拟机的映像类型。 |
|服务信息 1 |订阅上服务所属的项目名称。 |
|服务信息 2 |旧字段，用于捕获可选的服务特定元数据。 |

## <a name="how-do-i-make-a-payment"></a>如何付款？
如果已将信用卡或借记卡设置为付款方式，则会自动进行付款。 在信用卡对帐单上，行项会显示 **MSFT Azure**。

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>市场订单或外部服务收取多少费用？
外部服务过去称为市场订单。 外部服务由独立服务供应商提供，但已集成到 Azure 中。 

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。 
如果仍需要帮助，可 [联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 来快速解决问题。
 



