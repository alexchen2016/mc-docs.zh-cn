---
title: Azure 保留资源名称错误 | Azure
description: 描述了如何解决提供的资源名称包括保留字的错误。
services: azure-resource-manager
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
origin.date: 11/08/2017
ms.date: 11/27/2017
ms.author: v-yeche
ms.openlocfilehash: acd21835fe3b81c1e344f8a7e8cd86ab58d4443f
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663212"
---
# <a name="resolve-reserved-resource-name-errors"></a>解决保留资源名称错误

本文描述了在部署其名称中包含保留字的资源时遇到的错误。

## <a name="symptom"></a>症状

在部署可通过公共终结点访问的资源时，可能会收到以下错误：

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>原因

具有公共终结点的资源的名称中不能使用保留字或商标。

以下为保留字：

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

以下字词不能作为全字或子字符串在名称中使用：

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>解决方案

请提供一个不使用其中任一保留字的名称。

<!-- Update_Description: new articles on azure resource manager on reserved resource name -->