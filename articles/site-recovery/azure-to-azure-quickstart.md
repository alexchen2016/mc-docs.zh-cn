---
title: 为 Azure IaaS VM 设置到 Azure 次要区域的灾难恢复
description: 本快速入门提供了使用 Azure Site Recovery 服务在 Azure 区域之间对 Azure IaaS VM 进行灾难恢复所需的步骤。
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: quickstart
origin.date: 12/27/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 41d93645c5fedd64a87cc9457f5f84c9f2c24584
ms.sourcegitcommit: f1ecc209500946d4f185ed0d748615d14d4152a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463599"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>为 Azure VM 设置到 Azure 次要区域的灾难恢复        

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外中断期间使商业应用程序保持启动和运行状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本快速入门介绍如何将 Azure VM 复制到不同的 Azure 区域。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

> [!NOTE]
> 本文旨在引导新用户使用默认选项和最小的自定义来获取 Azure Site Recovery 体验。 若要详细了解可以自定义的各种设置，请参阅[为 Azure VM 启用复制的教程](azure-to-azure-tutorial-enable-replication.md)

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.cn 登录到 Azure 门户。

## <a name="enable-replication-for-the-azure-vm"></a>为 Azure VM 启用复制

1. 在 Azure 门户中，单击“虚拟机”，并选择要复制的 VM。
2. 在“操作”中，单击“灾难恢复”。
3. 在“配置灾难恢复” > “目标区域”中，选择要复制到的目标区域。
4. 在本快速入门中，接受其他默认设置。
5. 单击“启用复制”。 这将启动用于为 VM 启用复制的作业。

    ![启用复制](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>验证设置

复制作业完成后，可以检查复制状态、修改复制设置和测试部署。

1. 在 VM 菜单中，单击“灾难恢复”。
2. 可以验证复制运行状况、已创建的恢复点以及映射中的源和目标区域。

   ![复制状态](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>清理资源

对主要区域中的 VM 禁用复制时，该 VM 会停止复制：

- 将自动清除源复制设置。 请注意，作为复制的一部分安装的 Site Recovery 扩展未删除，需要手动删除。 
- 对 VM 的 Site Recovery 计费也会停止。

按如下所述停止复制

1. 选择 VM。
2. 在“灾难恢复”中，单击“禁用复制”。

   ![禁用复制](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，将单个 VM 复制到了次要区域。 现在可以浏览更多的选项并尝试按恢复计划复制一组 Azure VM。

> [!div class="nextstepaction"]
> [为 Azure VM 配置灾难恢复](azure-to-azure-tutorial-enable-replication.md)

<!-- Update_Description: update meta properties, wording update -->

