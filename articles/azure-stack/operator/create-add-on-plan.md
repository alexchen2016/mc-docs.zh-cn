---
title: 如何更新 Azure Stack 套餐和计划 | Microsoft Docs
description: 本文介绍如何查看和修改现有的 Azure Stack 套餐和计划。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
origin.date: 03/07/2019
ms.date: 03/18/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: aa05880ab79174548f66972e143cc977e0d28bd4
ms.sourcegitcommit: 05aa4e4870839a3145c1a3835b88cf5279ea9b32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64529988"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 附加计划

希望提供附加服务或将“计算机”、“存储”或“网络”配额的范围扩展到基本计划初始套餐之外时，Azure Stack 操作员可以创建附加计划来修改[基本计划](azure-stack-create-plan.md)。 附加计划对基本计划进行修改，是用户可以选择订阅的可选扩展。

有些情况下，将所有内容组合到单个计划中是最好的。 另一些情况下，你可能希望使用基本计划，然后通过使用附加计划来提供附加服务。 例如，可以通过基本计划提供 IaaS 服务，通过附加计划提供所有 PaaS 服务。

使用附加计划的另一个原因是帮助用户监视资源使用情况。 若要执行此操作，可以从一个包括相对较小的配额（取决于所需的服务）的基本计划开始。 然后，当用户达到容量时，将会向用户发送通知，指出他们已消耗完了根据其已分配计划分配的资源。 从那里，用户可以选择一个提供附加资源的附加计划。

> [!NOTE]
> 如果不希望使用附加计划来扩展配额，则还可以选择[编辑配额的原始配置](azure-stack-quota-types.md#edit-a-quota)。

将附加计划添加到现有的套餐订阅时，可能需要长达一小时的时间才会显示附加资源。

修改现有的套餐即可创建附加计划。

## <a name="create-an-add-on-plan-1902-and-later"></a>创建附加计划（1902 及更高版本）

1. 以云管理员身份登录到 Azure Stack 管理员门户。
2. 遵循[创建基本计划](azure-stack-create-plan.md)的步骤，创建此前尚未提供的新计划套餐服务。
3. 在管理员门户中单击“套餐”，然后选择需使用附加计划更新的套餐。

   ![创建附加计划](media/create-add-on-plan/add-on1.png)

4. 滚动到套餐属性的底部，选择“附加计划”。 单击“添加” 。

    ![创建附加计划](media/create-add-on-plan/add-on2.png)

5. 选择要添加的计划（ 在此示例中，计划名为“20-storageaccounts”。 选择计划后，单击“选择”将计划添加到套餐。 系统会发送通知，告知你计划已成功添加到套餐中。

    ![创建附加计划](media/create-add-on-plan/add-on3.png)

6. 查看随套餐提供的附加计划的列表，验证新附加计划是否已列出。

    [![创建附加计划](media/create-add-on-plan/add-on4.png "创建附加计划")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>创建附加计划（1901 及更低版本）

1. 以云管理员身份登录到 Azure Stack 管理员门户。
2. 遵循[创建基本计划](azure-stack-create-plan.md)的步骤，创建此前尚未提供的新计划套餐服务。 在此示例中，Key Vault (**Microsoft.KeyVault**) 服务将包含在新计划中。
3. 在管理员门户中单击“套餐”，然后选择需使用附加计划更新的套餐。

   ![创建附加计划](media/create-add-on-plan/1.PNG)

4. 滚动到套餐属性的底部，选择“附加计划”。 单击“添加” 。

    ![创建附加计划](media/create-add-on-plan/2.PNG)

5. 选择要添加的计划（ 在此示例中，计划名为“密钥保管库计划”。 选择计划后，单击“选择”将计划添加到套餐。 系统会发送通知，告知你计划已成功添加到套餐中。

    ![创建附加计划](media/create-add-on-plan/3.PNG)

6. 查看随套餐提供的附加计划的列表，验证新附加计划是否已列出。

    ![创建附加计划](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)