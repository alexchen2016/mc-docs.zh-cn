---
title: 用于 Azure 备份的 Azure 资源管理器模板
description: Azure 备份 PowerShell 示例
services: backup
author: lingliw
manager: digimobile
ms.service: backup
ms.topic: sample
ms.date: 01/21/19
ms.author: v-lingwu
ms.custom: mvc
ms.openlocfilehash: 1b96f677f68b820c555ebbef825ff0d3a51ff5fe
ms.sourcegitcommit: 26957f1f0cd708f4c9e6f18890861c44eb3f8adf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363481"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>用于 Azure 备份的 Azure 资源管理器模板

下表包含可以与恢复服务保管库和 Azure 备份功能配合使用的 Azure 资源管理器模板的链接。

|   |   |
|---|---|
|**恢复服务保管库** | |
| [创建恢复服务保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| 创建恢复服务保管库。 此保管库可以用于 Azure 备份和 Azure Site Recovery。 |
|**备份虚拟机**| |
| [备份资源管理器 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | 使用现有的恢复服务保管库和备份策略，备份同一资源组中的资源管理器虚拟机。|
| [将 IaaS VM 备份到恢复服务保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | 用于备份经典虚拟机和资源管理器虚拟机的模板。 |
| [为 IaaS VM 创建每周备份策略](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | 模板创建恢复服务保管库和每周备份策略，该策略用于备份经典虚拟机和资源管理器虚拟机。|
| [为 IaaS VM 创建每日备份策略](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | 模板创建恢复服务保管库和每日备份策略，该策略用于备份经典虚拟机和资源管理器虚拟机。|
| [部署启用了备份的 Windows Server VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | 模板创建启用默认备份策略的 Windows Server VM 和恢复服务保管库。|
|**监视备份作业** |  |
| [使用 Log Analytics 监视 Azure 备份](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | 模板部署用于 Azure 备份的 Log Analytics Monitoring，后者用于监视备份和还原作业、备份警报以及在恢复服务保管库中使用的云存储。|  
|   |   |


<!-- Update_Description: update metedata properties -->