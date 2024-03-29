---
title: 使用 Visual Studio 创建 Azure 云服务项目 | Microsoft Docs
description: 了解如何使用 Visual Studio 创建 Azure 云服务项目
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 03/21/2017
ms.date: 09/26/2018
ms.author: v-junlch
ms.openlocfilehash: 29425002a0e4e2c403cf7b1e7385c6d17962b568
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663593"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>使用 Visual Studio 创建 Azure 云服务项目
用于 Visual Studio 的 Azure 工具提供了可用于创建 [Azure 云服务](/cloud-services/cloud-services-choose-me)（一种简单的通用 Azure 服务）的项目模板。 创建项目后，可通过 Visual Studio 调试、配置云服务，并将其部署到 Azure。

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>在 Visual Studio 中创建 Azure 云服务项目的步骤
本节介绍如何在 Visual Studio 中创建具有一个或多个 Web 角色的 Azure 云服务项目。  

1. 以管理员身份启动 Visual Studio。

1. 在主菜单中，选择“文件” > “新建” > “项目”。

1. 从 Visual C# 或 Visual Basic 项目模板节点中选择“云”，并从模板列表中选择“Azure 云服务”。

    ![新建 Azure 云服务](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. 指定要用于开发项目的 .NET Framework 版本。

1. 输入项目的名称和位置以及解决方案的名称。 

1. 选择“确定” 。

1. 在“新建 Azure 云服务”对话框中，选择要添加的角色，并选择右箭头按钮以将其添加到解决方案。

    ![选择新的 Azure 云服务角色](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. 如果要重命名已添加的角色，请在“新建 Azure 云服务”对话框中将鼠标悬停在该角色上，并从上下文菜单中选择“重命名”。 还可在添加角色后在解决方案（**解决方案资源管理器**中）内对其进行重命名。

    ![重命名 Azure 云服务角色](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Visual Studio Azure 项目与解决方案中的角色项目具有关联。 该项目还包括服务定义文件和服务配置文件：

- **服务定义文件** - 定义了应用程序的运行时设置，包括所需角色、终结点和虚拟机大小。 
- **服务配置文件** - 配置了角色有多少实例在运行以及为角色定义的设置的值。 

有关这些文件的详细信息，请参阅[使用 Visual Studio 配置 Azure 云服务的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。

## <a name="next-steps"></a>后续步骤
- [使用 Visual Studio 管理 Azure 云服务项目中的角色](./vs-azure-tools-cloud-service-project-managing-roles.md)

<!-- Update_Description: wording update -->