---
title: 使用 Azure Stack 门户部署模板 | Microsoft Docs
description: 了解如何使用 Azure Stack 门户部署模板。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 05/07/2019
ms.date: 06/03/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: bb80eb3f5ca4f8212850b936ea14f958fe0baccb
ms.sourcegitcommit: 77d6ceb6a14a3316a6088859c4d9978115b2454a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248543"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 门户部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用门户将 Azure 资源管理器模板部署到 Azure Stack。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到门户中，依次选择“+ 创建资源”  、“自定义”  。

   ![创建](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. 选择“模板部署”  。

   ![部署模板](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. 选择“编辑模板”  ，然后将 JSON 模板代码粘贴到代码窗口中。 选择“其他安全性验证”  。

   ![编辑模板](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. 选择“编辑参数”  ，为显示的参数提供值，然后选择“确定”  。

   ![编辑参数](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. 选择“订阅”  。 选择要使用的订阅，然后选择“确定”  。

   ![订阅](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. 选择“资源组”  。 选择现有资源组，或创建一个新资源组，然后选择“确定”  。

   ![资源组](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. 选择“创建”  。 仪表板上的新磁贴会跟踪模板部署的进度。

   ![创建模板](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>后续步骤

若要了解有关部署模板的详细信息，请参阅以下文章：

- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
