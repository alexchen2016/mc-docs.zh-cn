---
title: Azure Stack 发行说明 - 更新活动清单 | Microsoft Docs
description: 为系统准备最新 Azure Stack 更新的快速清单。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/02/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: ''
ms.lastreviewed: 05/02/2019
ms.openlocfilehash: 098e7fb4ba58ec9318ce24307e5b93cfef1bb772
ms.sourcegitcommit: 77d6ceb6a14a3316a6088859c4d9978115b2454a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66249157"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack 更新活动清单

本文包含 Azure Stack 操作员的更新相关活动的清单。 如果你正准备将更新应用于 Azure Stack，可以查看此信息。

## <a name="prepare-for-azure-stack-update"></a>准备 Azure Stack 更新

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 查看已知问题   | [已知问题列表](azure-stack-release-notes-known-issues-1904.md)。                |
| 查看安全更新 | [安全更新列表](azure-stack-release-notes-security-updates-1904.md)。      |
| 运行 Test-AzureStack   | 运行 `Test-AzureStack -Group UpdateReadiness` 确定操作问题。      |
| 解决问题        | 解决 **Test-AzureStack** 确定的任何操作问题。                |
| 应用最新修补程序 | 应用适用于当前安装版本的最新修补程序。         |
| 运行 Capacity Planner 工具 | 请确保使用最新版本的 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner)  工具来执行工作负荷规划和大小调整。 最新版本包含 bug 修复，并提供与每个 Azure Stack 更新一起发布的新功能。 |
| 可用更新       | 只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 断开连接的客户可以根据[此处所述的过程](azure-stack-apply-updates.md)下载和导入新的 1904 包。               |

## <a name="during-azure-stack-update"></a>在 Azure Stack 更新期间

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 管理更新         | [使用运营商门户管理 Azure Stack 中的更新](azure-stack-updates.md)。 |
| 监视更新        | 如果运营商门户不可用，请[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |
| 恢复更新            | 在修复失败的更新后，[使用特权终结点恢复 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |

> [!IMPORTANT]  
> 在更新期间不要运行 **Test-AzureStack**，因为这会导致更新停止。

## <a name="after-azure-stack-update"></a>Azure Stack 更新后

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 应用最新修补程序 | 应用适用于已更新版本的最新修补程序。                          |
| 检索加密密钥 | 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。 |

## <a name="next-steps"></a>后续步骤

- [查看已知问题列表](azure-stack-release-notes-known-issues-1904.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1904.md)
