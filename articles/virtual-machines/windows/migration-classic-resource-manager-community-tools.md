---
title: 社区工具 - 将经典资源迁移到 Azure Resource Manager | Azure
description: 本文编录了社区提供的工具，这些工具可帮助将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 03/30/2017
ms.date: 05/21/2018
ms.author: v-yeche
ms.openlocfilehash: bce4992307af1dc2157f62335ec9c2964c9d92b1
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52659089"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>使用社区工具将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器
本文编录了社区提供的工具，这些工具可帮助将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。

> [!NOTE]
> Azure.cn 支持服务并不正式支持这些工具。 因此 GitHub 上提供了其开放源代码，我们很乐意接受有关修复或其他方案的 PR。 若要报告问题，请使用 GitHub 问题功能。
> 
> 使用这些工具进行迁移会造成经典虚拟机停机。 若要了解平台支持的迁移，请访问 
> 
>   * [平台支持从经典部署模型迁移到 Azure Resource Manager 堆栈的 IaaS 资源](migration-classic-resource-manager-overview.md)
>   * [有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
>   * [使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
> 
> 
<!-- Notice: Azure changed to Azure.cn-->

## <a name="asmmetadataparser"></a>AsmMetadataParser
这是一个帮助程序工具集合，它是在从 Azure 服务管理到 Azure Resource Manager 的企业迁移过程中创建的。 借助此工具，可将基础结构复制到另一个订阅，可将其用于在生产订阅中运行迁移前测试迁移并消除所有问题。

[工具文档链接](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz 是另一选项，用于将整套经典 IaaS 资源迁移到 Azure Resource Manager IaaS 资源。 可以在同一订阅中迁移，也可以在不同订阅和不同订阅类型（例如 CSP 订阅）中迁移。

[工具文档链接](https://github.com/Azure/migAz)

## <a name="next-steps"></a>后续步骤

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](../linux/migration-classic-resource-manager-cli.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)
<!-- Update_Description: update meta properties -->