---
title: 在 Azure VM 上排查附加 VHD 的 VM 的意外重启 | Azure
description: 如何排查 VM 的意外重启。
keywords: ssh 连接被拒绝, ssh 错误, azure ssh, SSH 连接失败
services: virtual-machines
author: rockboyfor
manager: digimobile
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
origin.date: 11/01/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: aa8d79403f800cc7bafce26623297eb82da01880
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675084"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>排查附加 VHD 的 VM 意外重启

如果 Azure 虚拟机 (VM) 具有大量位于同一存储帐户的附加 VHD，则可能会超过单个存储帐户的可伸缩性目标，从而导致 VM 意外重启。 检查存储帐户的分钟指标 (**TotalRequests**/**TotalIngress**/**TotalEgress**)，了解超过存储帐户的可伸缩性目标的峰值。 若要帮助确定是否已对存储帐户进行限制，请参阅[指标显示 PercentThrottlingError 增加](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError)。

通常，虚拟机对 VHD 进行的每个单独的输入或输出操作都会转换为对基础页 Blob 进行的“Get 页”或“Put 页”操作。 因此，可以根据应用程序的特定行为，对环境使用估计的 IOPS 以优化可以在单个存储帐户中设置的 VHD 数。 Azure 建议在单个存储帐户中设置 40 个或更少的磁盘。 有关存储帐户的可伸缩性目标的详细信息（尤其是使用的存储帐户类型的总请求速率和总带宽），请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。

如果即将超过存储帐户的可伸缩性目标，请将 VHD 放入多个存储帐户中，以减少每个帐户中的活动。

<!-- Update_Description: update meta properties -->