---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52651004"
---
如果 Python 在启动应用程序时遇到错误，只只会返回简单的错误页（例如“由于发生内部服务器错误，无法显示该页。”）。

捕获 Python 应用程序错误：

1. 在 Azure 门户中的 Web 应用中，选择“设置”。
2. 在“设置”选项卡上，选择“应用程序设置”。
3. 在“应用设置”下，输入以下键/值对：
    * 键：WSGI_LOG
    * 值：D:\home\site\wwwroot\logs.txt（输入所选文件名）

现在应可在 wwwroot 文件夹中的 logs.txt 文件中看到错误。
