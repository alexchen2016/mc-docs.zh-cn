---
title: 将服务主体添加到 Azure Analysis Services 服务器管理员角色 | Azure
description: 了解如何将自动化服务主体添加到服务器管理员角色
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: b2b845bf3441e02592cf97978b654ef92717b823
ms.sourcegitcommit: edce097f471b6e9427718f0641ee2b421e3c0ed2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58348047"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>将服务主体添加到服务器管理员角色 

 若要自动执行无人参与的 PowerShell 任务，服务主体在托管的 Analysis Services 服务器上必须具备“服务器管理员”权限。 本文介绍如何将服务主体添加到 Azure AS 服务器上的服务器管理员角色。

## <a name="before-you-begin"></a>准备阶段
在完成此项任务之前，必须有一个在 Azure Active Directory 中注册的服务主体。

[创建服务主体 - Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)   
[创建服务主体 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>所需的权限
若要完成此项任务，在 Azure AS 服务器上必须具备[服务器管理员](analysis-services-server-admins.md)权限。 

## <a name="add-service-principal-to-server-administrators-role"></a>将服务主体添加到服务器管理员角色

1. 在 SSMS 中连接至 Azure AS 服务器。
2. 在“服务器属性” > “安全性”中，单击“添加”。
3. 在“选择用户或组”中，按名称搜索注册的应用，选中以后单击“添加”。

    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 验证服务主体帐户 ID，然后单击“确定”。

    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

> [!NOTE]
> 对于使用 Azure PowerShell cmdlet 进行的服务器操作，运行计划程序的服务主体还必须属于 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 中资源的“所有者”角色。 

## <a name="related-information"></a>相关信息

* [下载 SQL Server PowerShell 模块](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下载 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

<!-- Update_Description:  update meta properties, update link -->