---
title: 使用 Visual Studio 访问私有 Azure 云 | Microsoft Docs
description: 了解如何通过使用 Visual Studio 访问私有云资源。
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 11/13/2017
ms.date: 09/10/2018
ms.author: v-junlch
ms.openlocfilehash: 3a5fd8fdd4111e93a873467e3836605def2322c5
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52645878"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>使用 Visual Studio 访问私有 Azure 云

默认情况下，Visual Studio 支持 Azure 云 REST 终结点。 在本文中，介绍如何使用私有云证书访问 Visual Studio 中的私有云并与之交互。

1. 在私有云的 Azure 门户中下载发布设置文件，或联系管理员获取发布设置文件。 （该文件的扩展名为 `.publishsettings`。）

1. 在 Visual Studio 服务器资源管理器中，右键单击 Azure 节点，并选择“管理和筛选订阅”。

    ![管理订阅命令](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. 在“管理 Azure 订阅”对话框中，选择“证书”选项卡，然后选择“导入”。

    ![导入 Azure 证书](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. 在“导入 Azure 订阅”对话框中，选择“浏览”。

    ![“导入 Azure 订阅”对话框中的“浏览”按钮](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. 在“打开”对话框中，浏览到保存发布设置文件的目录，再选择文件，然后选择“打开”。

    ![选择发布设置文件](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. 返回到“导入 Azure 订阅”对话框时，选择“导入”。

    ![导入发布设置文件](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    证书已从发布设置文件导入到 Visual Studio 中，现可与私有云资源进行交互。


<!-- Update_Description: update metedata properties -->