---
title: 了解使用服务和开发应用时 Azure 与 Azure Stack 之间的主要差异 | Microsoft Docs
description: 使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用时需要了解的差异。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
origin.date: 04/08/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 25b7da3485c0da1e2c583c0db0e8826df11f07f4
ms.sourcegitcommit: 20bff6864fd10596b5fc2ac8e059629999da8ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135477"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>重要注意事项：使用 Azure Stack 的服务或开发 Azure Stack 的应用

使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用之前，必须了解 Azure Stack 与 Azure 之间的差异。 本文阐述在将 Azure Stack 用作混合云开发环境时的重要注意事项。

## <a name="overview"></a>概述

Azure Stack 是一个混合云平台，可用于通过公司或服务提供商的数据中心使用 Azure 服务。 可在 Azure Stack 上开发应用，然后将其部署到 Azure Stack、Azure 或 Azure 混合云。

Azure Stack 运营商会告知有哪些服务可用，以及如何获取支持。 他们通过自定义计划和套餐来提供这些服务。

Azure 技术文档内容假设应用是为 Azure 服务（而不是 Azure Stack）开发的。 开发应用并将其部署到 Azure Stack 时，必须了解一些主要差异，例如：

* Azure Stack 提供 Azure 所提供的一部分服务和功能。
* 你的公司或服务提供商可以选择他们想要提供的服务。 可用的选项可能包括自定义的服务或应用程序。 他们可以提供自己的自定义文档。
* 必须使用正确的 Azure Stack 特定终结点（例如，门户地址和 Azure 资源管理器终结点的 URL）。
* 必须使用 Azure Stack 支持的 PowerShell 和 API 版本。 使用支持的版本可确保应用在 Azure Stack 和 Azure 中运行。

## <a name="cheat-sheet-high-level-differences"></a>速查表：大致差异

下表描述了 Azure Stack 与 Azure 之间的大致差异。 开发适用于 Azure Stack 的应用或使用 Azure Stack 服务时，请记住这些差异。

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

| 区域 | Azure（中国） | Azure Stack |
| -------- | ------------- | ----------|
| 由谁运营？ | 世纪互联 | 你的组织或服务提供商。|
| 寻求支持的联系对象是谁？ | Azure | 对于集成环境，请联系你的组织或服务提供商的 Azure Stack 运营商来获取支持。<br><br>如需获取 Azure Stack 开发工具包支持，请访问 [Azure 论坛](https://social.msdn.microsoft.com/Forums/zh-cn/home)。 由于开发工具包是一个评估环境，因此我们不会通过 Azure 客户支持服务 (CSS) 提供官方支持。
| 可用服务 | 参阅 [Azure 产品](https://www.azure.cn/zh-cn/home/features/products-by-region)列表。 可用服务因 Azure 区域而异。 | Azure Stack 支持部分 Azure 服务。 实际服务因你的组织或服务提供商选择提供的服务而异。
| Azure 资源管理器终结点* | https://management.chinacloudapi.cn | 对于 Azure Stack 集成系统，请使用 Azure Stack 运营商提供的终结点。<br><br>对于开发工具包，请使用： https://management.local.azurestack.external
| 门户 URL* | [https://portal.azure.cn](https://portal.azure.cn) | 对于 Azure Stack 集成系统，请使用 Azure Stack 运营商提供的 URL。<br><br>对于开发工具包，请使用： https://portal.local.azurestack.external
| 区域 | 可以选择要部署到的区域。 | 对于 Azure Stack 集成系统，请使用系统中可用的区域。<br><br>对于开发工具包，区域始终为**本地**。
| 资源组 | 资源组可以跨区域。 | 对于集成系统和开发工具包，只有一个区域。
|支持的命名空间、资源类型和 API 版本 | 最新版本（或未弃用的较低版本）。 | Azure Stack 支持特定的版本。 请参阅本文的[版本要求](#version-requirements)部分。
| | |

*如果你是 Azure Stack 运营商，请参阅[使用管理员门户](../operator/azure-stack-manage-portals.md)和[管理基础知识](../operator/azure-stack-manage-basics.md)来了解详细信息。

## <a name="helpful-tools-and-best-practices"></a>有用的工具和最佳做法

Azure 提供的工具和指导有助于 Azure Stack 方面的开发。

| 建议 | 参考 |
| -------- | ------------- |
| 在开发人员工作站上安装正确的工具。 | - [安装 PowerShell](../operator/azure-stack-powershell-install.md)<br>- [下载工具](../operator/azure-stack-powershell-download.md)<br>- [配置 PowerShell](azure-stack-powershell-configure-user.md)<br>- [安装 Visual Studio](azure-stack-install-visual-studio.md) 
| 查看有关以下各项的信息：<br>- Azure 资源管理器模板注意事项<br>- 如何查找快速入门模板<br>- 借助策略模块使用 Azure 进行 Azure Stack 方面的开发 | [Azure Stack 开发](azure-stack-developer.md) | 
| 查看并遵循模板最佳做法。 | [资源管理器快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>版本要求

Azure Stack 支持特定版本的 Azure PowerShell 和 Azure 服务 API。 使用支持的版本，确保可将应用部署到 Azure Stack 和 Azure。

若要确保使用正确版本的 Azure PowerShell，请使用 [API 版本配置文件](azure-stack-version-profiles.md)。 若要确定可以使用的最新 API 版本配置文件，请找出使用的 Azure Stack 内部版本。 可以从 Azure Stack 管理员获取此信息。

> [!NOTE]
> 如果使用 Azure Stack 开发工具包，并且拥有管理访问权限，请参阅[确定当前版本](../operator/azure-stack-updates.md#determine-the-current-version)部分来确定 Azure Stack 内部版本。

对于其他 API，请运行以下 PowerShell 命令输出 Azure Stack 订阅中支持的命名空间、资源类型和 API 版本。 请注意，可能仍存在属性级别的差异。 若要正常运行此命令，必须事先[安装](../operator/azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-user.md)适用于 Azure Stack 环境的 PowerShell。 此外，必须有 Azure Stack 套餐的订阅。

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

示例输出（已截断）：![Get-AzureRmResourceProvider 命令的示例输出](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>后续步骤

有关服务级别的差异的详细信息，请参阅：

* [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)
* [Azure Stack 中的存储注意事项](azure-stack-acs-differences.md)
* [Azure Stack 网络注意事项](azure-stack-network-differences.md)
