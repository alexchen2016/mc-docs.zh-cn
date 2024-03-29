---
title: 在 Visual Studio 中使用连接服务添加 Azure 存储 | Microsoft Docs
description: 使用 Visual Studio 的“添加连接服务”对话框将 Azure 存储添加到你的应用
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 03/26/2017
ms.date: 09/10/2018
ms.author: v-junlch
ms.openlocfilehash: e20534971ae105a2cb9af22df55e0b2ebb9cd7c7
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52662748"
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务添加 Azure 存储
在 Visual Studio 中，通过使用“添加连接服务”对话框可将以下任何服务连接到 Azure 存储：

- C# 云服务
- .NET 后端移动服务
- ASP.NET 网站或服务
- ASP.NET Core 服务
- Azure WebJob 服务 

连接服务功能可将所有需要的引用和连接代码添加到项目，并相应地修改配置文件。 

完成后，“添加连接服务”对话框会自动显示文档，其中详细介绍开始使用 blob 存储、队列和表所需的步骤。

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>使用“连接服务”对话框连接到 Azure 存储
1. 在 Visual Studio 中打开项目

1. 在“解决方案资源管理器”中，右键单击“连接服务”节点，并在上下文菜单中选择“添加连接服务”。
   
    ![添加 Azure 连接服务](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. 在“连接服务”页面中，选择“Azure 存储的云存储”。
   
    ![添加 Azure 存储](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. 在“Azure 存储”对话框中，选择一个现有的存储帐户，并选择“添加”。
   
    如果需要创建存储帐户，请转到下一步。 否则，请跳到步骤 6。
    
    ![将现有存储帐户添加到项目](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. 创建存储帐户： 
   
   1. 选择对话框底部的“创建新存储帐户”。

   1. 填写“创建存储帐户”对话框，并选择“创建”。
      
       ![新的 Azure 存储帐户](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. 显示“Azure 存储”对话框时，新的存储帐户会显示在列表中。 在列表中选择新存储帐户，并选择“添加”。

1. 该存储连接服务会显示在项目的“服务引用”节点下。
   
## <a name="how-your-project-is-modified"></a>项目的修改情况
完成该对话框后，Visual Studio 会添加引用并修改特定配置文件。 具体更改情况取决于项目类型： 

- ASP.NET 项目 - [完成的操作 - ASP.NET 项目](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- ASP.NET Core 项目 - [完成的操作 - ASP.NET 5 项目](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- 云服务项目（Web 角色和辅助角色）- [完成的操作 - 云服务项目](http://go.microsoft.com/fwlink/p/?LinkId=516965)

## <a name="next-steps"></a>后续步骤
- [MSDN 论坛：Azure 存储](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure 存储文档](/storage/)

<!-- Update_Description: link update -->