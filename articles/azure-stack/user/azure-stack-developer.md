---
title: 开发适用于 Azure Stack 的应用 | Microsoft Docs
description: 在 Azure Stack 中制作应用程序原型的开发注意事项
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: bcf5c4ccc864339341763963ece48044b6d640e7
ms.sourcegitcommit: 6101e77a8a4b8285ddedcb5a0a56cd3884165de9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218254"
---
# <a name="develop-for-azure-stack"></a>为 Azure Stack 进行开发

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

即使没有 Azure Stack 环境的访问权限，也可以立即开始开发应用程序。 由于 Azure Stack 交付在数据中心运行的 Azure 服务，因此可以使用类似工具和流程针对 Azure Stack 进行开发，就如同为 Azure 进行开发一样。 

## <a name="development-considerations"></a>开发注意事项

通过一些准备工作并使用以下主题中的指导，可以使用 Azure 来模拟 Azure Stack 环境。

* 在 Azure 中，可以创建可部署到 Azure Stack 的 Azure 资源管理器模板。 请参阅[模板注意事项](azure-stack-develop-templates.md)，获取有关开发模板的指导，以确保可移植性。
* Azure 与 Azure Stack 之间的服务可用性和服务版本控制存在差异。 可以使用 [Azure Stack 策略模块](azure-stack-policy-module.md)来限制 Azure Stack 可用的 Azure 服务可用性和资源类型。 约束服务可确保应用程序依赖可用于 Azure Stack 的服务。
* [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)是常见的方案示例，演示了如何开发可部署到 Azure 和 Azure Stack 的模板。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 开发的详细信息，请参阅以下文章：

* [Azure 资源管理器模板最佳做法](azure-stack-develop-templates.md)
* [GitHub 上的 Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)