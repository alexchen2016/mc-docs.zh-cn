---
title: 由于 NIC 被禁用而无法远程连接到 Azure 虚拟机 | Azure
description: 了解因为 NIC 在 Azure VM 中被禁用而导致 RDP 失败时如何进行故障排除 | Azure
services: virtual-machines-windows
documentationCenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 11/12/2018
ms.date: 12/24/2018
ms.author: v-yeche
ms.openlocfilehash: 9bf8eb701b2713a31d0993d7e2a45b04e7b81aa5
ms.sourcegitcommit: 96ceb27357f624536228af537b482df08c722a72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736240"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>无法通过远程桌面连接到 VM，因为网络接口被禁用

本文介绍如何解决在禁用网络接口时无法与 Azure Windows 虚拟机 (VM) 建立远程桌面连接的问题。

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用资源管理器部署模型。建议对新部署使用该模型，而不要使用经典部署模型。

## <a name="symptoms"></a>症状

无法与 Azure 中的 VM 的任何其他端口建立 RDP 连接或任何其他类型的连接，因为 VM 中的网络接口被禁用。

## <a name="solution"></a>解决方案

在执行这些步骤之前，请创建受影响 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要为 VM 启用该接口，请为 VM [重置网络接口](##reset-network-interface)。

<!-- Not Available on use Serial control or -->
<!-- Not Available on ### Use Serial control-->
## <a name="reset-network-interface"></a>重置网络接口

要重置网络接口，请将 IP 地址更改为子网中可用的其他 IP 地址。 若要执行此操作，请使用 Azure 门户或 Azure PowerShell。 有关详细信息，请参阅[重置网络接口](reset-network-interface.md)。

<!-- Update_Description: new articles on troubleshoot rdp nic disabled -->
<!--ms.date: 12/24/2018-->