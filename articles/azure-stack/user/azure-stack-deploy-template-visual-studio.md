---
title: 在 Azure Stack 中使用 Visual Studio 部署模板 | Microsoft 文档
description: 了解如何在 Azure Stack 中使用 Visual Studio 部署模板。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 7519b2a612174e061804a9a22eab2b29af83bd7c
ms.sourcegitcommit: 6101e77a8a4b8285ddedcb5a0a56cd3884165de9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218301"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>使用 Visual Studio 在 Azure Stack 中部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Visual Studio 将 Azure 资源管理器模板部署到 Azure Stack。

## <a name="to-deploy-a-template"></a>部署模板

1. 使用 Visual Studio [安装并连接](azure-stack-install-visual-studio.md)到 Azure Stack。
2. 打开 Visual Studio。
3. 选择“文件”，然后选择“新建”。 在“新建项目”中，选择“Azure 资源组”。
4. 输入新项目的名称，并选择“确定”。
5. 在“选择 Azure 模板”中，从下拉列表中选取“Azure Stack 快速入门”。
6. 选择 **101-create-storage-account**，然后选择“确定”。
7. 在新项目中，展开**解决方案资源管理器**中的“模板”节点以查看可用模板。
8. 在**解决方案资源管理器**中，选择你的项目名称，然后选择“部署”。 选择“新建部署”。
9. 在“部署到资源组”中，使用“订阅”下拉列表选择 Azure Stack 订阅。
10. 从“资源组”列表中选择现有资源组，或创建新资源组。
11. 从“资源组位置”列表中选择一个位置，并选择“部署”。
12. 在“编辑参数”中，为参数提供值（因模板而异），然后选择“保存”。

## <a name="next-steps"></a>后续步骤

* [通过命令行部署模板](azure-stack-deploy-template-command-line.md)
* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)

<!-- Update_Description: wording update -->