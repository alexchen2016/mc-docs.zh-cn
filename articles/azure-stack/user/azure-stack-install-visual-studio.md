---
title: 安装 Visual Studio 并连接到 Azure Stack | Microsoft Docs
description: 了解安装 Visual Studio 并连接到 Azure Stack 所需的步骤
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/08/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 784b35f06a57d9e2201a02d7b1de43a3d708cf78
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854912"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>安装 Visual Studio 并连接到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Visual Studio 将 Azure 资源管理器[模板](azure-stack-arm-templates.md)写入和部署到 Azure Stack。 本文中的步骤介绍如何在 [Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) 上或者在外部计算机上（如果你打算通过 [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) 来使用 Azure Stack）安装 Visual Studio。

## <a name="install-visual-studio"></a>安装 Visual Studio

1. 下载并运行此 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。  

2. 打开“Microsoft Web 平台安装程序”。

3. 搜索 **Visual Studio Community 2015 with Azure SDK - 2.9.6**。 依次单击“添加”、“安装”。

4. 卸载作为 Azure SDK 的一部分安装的 **Azure PowerShell**。

    ![WebPI 安装步骤的屏幕截图](./media/azure-stack-install-visual-studio/image1.png)

5. [安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)

6. 安装完成后，重启操作系统。

## <a name="connect-to-azure-stack-with-azure-ad"></a>使用 Azure AD 连接到 Azure Stack

1. 启动 Visual Studio。

2. 在“视图”菜单中，选择“云资源管理器”。

3. 在新窗格中选择“添加帐户”，然后使用 Azure Active Directory (Azure AD) 凭据登录。  

    ![登录并连接到 Azure Stack 后的 Cloud Explorer 屏幕截图](./media/azure-stack-install-visual-studio/image2.png)

登录后，可以[部署模板](azure-stack-deploy-template-visual-studio.md)或浏览可用的资源类型和资源组以创建自己的模板。  

## <a name="connect-to-azure-stack-with-ad-fs"></a>使用 AD FS 连接到 Azure Stack

1. 启动 Visual Studio。

2. 在“工具”中选择“选项”。

3. 展开**导航窗格**中的“环境”，然后选择“帐户”。

4. 选择“添加”，然后输入“用户 Azure 资源管理器终结点”。 对于 Azure Stack 开发工具包，URL 为：`https://management.local.azurestack/external`。  对于 Azure Stack 集成系统，URL 为：`https://management.[Region}.[External FQDN]`。

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. 选择“设置” （应用程序对象和服务主体对象）。  

    Visual Studio 会调用 Azure 资源管理器，并发现终结点，包括 Active Directory 联合身份验证服务 (AD FS) 的身份验证终结点。

    ![登录并连接到 Azure Stack 后的 Cloud Explorer 屏幕截图](./media/azure-stack-install-visual-studio/image6.png)

6. 在“视图”菜单中，选择“Cloud Explorer”。

7. 选择“添加帐户”并使用 AD FS 凭据登录。  

    ![云资源管理器](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer 将查询可用的订阅。 可以选择一个要管理的可用订阅。

    ![云资源管理器](./media/azure-stack-install-visual-studio/image8.png)

8. 浏览现有的资源或资源组，或者部署模板。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[并行安装](https://docs.microsoft.com/visualstudio/install/install-visual-studio-versions-side-by-side) Visual Studio 与其他 Visual Studio 版本。
- [为 Azure Stack 开发模板](azure-stack-develop-templates.md)。
 
<!-- Update_Description: wording update -->