---
title: 在 Azure 应用服务移动应用中创建 Android 应用
description: 遵循本教程开始使用 Azure 移动应用后端进行 Android 开发
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
origin.date: 10/01/2016
ms.date: 06/17/2019
ms.author: v-biyu
ms.openlocfilehash: 5eb06d8c8edc175b0b802699e352565783e155a1
ms.sourcegitcommit: d7db02d1b62c7b4deebd5989be97326b4425d1d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687471"
---
# <a name="create-an-android-app"></a>创建 Android 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程演示如何使用 Azure 移动应用后端向 Android 移动应用添加基于云的后端服务。  将创建一个新的移动应用后端和一个简单的 *待办事项列表* Android 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关使用 Azure 应用服务中的移动应用功能的所有其他 Android 教程。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要以下各项：

* [Android 开发人员工具](https://developer.android.com/sdk/index.html)，其中包含 Android Studio 集成开发环境和最新的 Android 平台。
* Azure 移动 Android SDK。
* [有效的 Azure 帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="create-a-new-azure-mobile-app-backend"></a>创建新的 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>运行 Android 应用
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.cn/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203