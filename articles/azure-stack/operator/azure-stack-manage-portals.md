---
title: 在 Azure Stack 中使用管理门户 | Microsoft Docs
description: 向 Azure Stack 操作员介绍管理门户的用法。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
origin.date: 02/25/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: a8c80c7e19d549ec614b502700cca50c114a6408
ms.sourcegitcommit: 20bff6864fd10596b5fc2ac8e059629999da8ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135480"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>快速入门：使用 Azure Stack 管理门户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 中有两种门户：管理门户和用户门户（有时称作租户门户）。  Azure Stack 操作员可以使用管理门户进行日常的 Azure Stack 管理和操作。

## <a name="access-the-administrator-portal"></a>访问管理员门户

若要访问管理员门户，请浏览到门户 URL，然后使用 Azure Stack 操作员的凭据登录。 对于集成系统，门户 URL 根据 Azure Stack 部署的区域名称和外部完全限定域名 (FQDN) 而有所不同。 Azure Stack 开发工具包 (ASDK) 部署的管理门户 URL 始终是相同的。 

| 环境 | 管理员门户 URL |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| 集成系统 | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> 对于 ASDK 环境，首先需确保可以通过远程桌面连接或虚拟专用网络 (VPN) [连接到开发工具包主机](../asdk/asdk-connect.md)。

 ![管理门户](media/azure-stack-manage-portals/admin-portal.png)

所有 Azure Stack 部署的默认时区都设置为协调世界时 (UTC)。 

在管理员门户中，可以执行如下所述的操作：

* [将 Azure Stack 注册到 Azure](azure-stack-registration.md)
* [充实市场](azure-stack-download-azure-marketplace-item.md)
* [为用户创建计划、套餐和订阅](azure-stack-plan-offer-quota-overview.md)
* [监视运行状况和警报](azure-stack-monitor-health.md)
* [管理 Azure Stack 更新](azure-stack-updates.md)

“快速入门教程”磁贴提供最常见任务的联机文档链接。 

尽管操作员可在管理门户中创建虚拟机、虚拟网络和存储帐户等资源，但应该[登录到用户门户](../user/azure-stack-use-portal.md)来创建和测试资源。

>[!NOTE]
>使用快速入门教程磁贴中的“创建虚拟机”链接可在管理员门户中创建虚拟机，但这只是用于验证 Azure Stack 部署是否成功。 

## <a name="understand-subscription-behavior"></a>了解订阅行为

默认会在管理门户中创建三个订阅：消耗、默认提供者和计量。 操作员主要使用“默认提供者订阅”。  无法添加其他订阅并在管理门户中使用它们。 

用户可以在用户门户中根据你为他们创建的计划和套餐创建其他订阅。 但是，在用户门户中无法访问管理门户的任何管理或操作功能。

管理门户和用户门户基于 Azure 资源管理器的独立实例。 由于 Azure 资源管理器的分隔性，订阅不会跨门户。 例如，如果以 Azure Stack 操作员的身份登录到用户门户，则无法访问默认提供商订阅。  虽然无法访问任何管理功能，但你可以通过提供的公共套餐为自己创建订阅。 只要你登录到用户门户，系统就会将你视为租户用户。

  >[!NOTE]
  >在 ASDK 环境中，如果某个用户与 Azure Stack 操作员属于同一个租户目录，则系统不会阻止他们登录到管理门户。 但是，他们无法访问任何管理功能，或者添加订阅来访问用户门户中可供他们使用的套餐。

## <a name="administration-portal-tips"></a>管理门户提示

### <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 可以选择“编辑仪表板”  来修改默认仪表板，或者选择“新建仪表板”来添加自定义仪表板。  可以轻松地将磁贴添加到仪表板中。 例如，可以选择“+ 创建资源”，右键单击“套餐 + 计划”，然后选择“固定到仪表板”。   

有时可能会在门户中看到空白的仪表板。 若要恢复仪表板，请单击“编辑仪表板”，然后单击右键并选择“重置为默认状态”。  

### <a name="quick-access-to-online-documentation"></a>快速访问联机文档

若要访问 Azure Stack 操作员文档，请使用管理员门户右上角的“帮助和支持”图标（问号）。 将鼠标移至该图标，然后选择“帮助 + 支持”。 

### <a name="quick-access-to-help-and-support"></a>快速访问帮助和支持内容

如果选择管理员门户右上角的“帮助和支持”图标（问号），然后选择“新建支持请求”，则会出现以下结果之一： 

- 如果使用的是集成系统，此操作会打开一个站点，可在其中直接向 Azure 客户支持服务 (CSS) 创建支持票证。 若要了解何时应该获取 Azure 支持或原始设备制造商 (OEM) 硬件供应商支持，请参阅[在何处获取支持](azure-stack-manage-basics.md#where-to-get-support)。
- 如果使用的是 ASDK，则此操作会直接打开 [Azure Stack 论坛站点](https://social.msdn.microsoft.com/Forums/zh-CN/home?forum=AzureStack)。 我们会定期关注这些论坛。 由于 ASDK 是一个评估环境，因此我们不会通过 Azure CSS 提供官方支持。

## <a name="next-steps"></a>后续步骤

[将 Azure Stack 注册到 Azure](azure-stack-registration.md)，并使用提供给用户的项充实 [Azure Stack 市场](azure-stack-marketplace.md)。 

<!-- Update_Description: wording update -->