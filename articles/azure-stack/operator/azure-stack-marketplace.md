---
title: 在 Azure Stack 中发布自定义的市场项（面向云操作员）| Microsoft Docs
description: 向 Azure Stack 操作员介绍了解如何在 Azure Stack 中发布自定义的市场项。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
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
ms.openlocfilehash: 59cab6746a968ebe9f5eeb9857a3ee5cf9550f3e
ms.sourcegitcommit: 87e9b389e59e0d8f446714051e52e3c26657ad52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66381874"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack 市场概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 市场是一组针对 Azure Stack 自定义的服务、应用程序和资源集合。 资源包括网络、虚拟机、存储，等等。 使用市场可以创建新资源和部署新应用程序，或者浏览并选择想要使用的项。 若要使用某个市场项，用户必须订阅一个可让他们访问该项的产品/服务。

Azure Stack 操作员可以确定要添加（发布）到市场的项。 可以发布数据库、应用服务等项。 发布项可让所有用户看到它们。 可以发布创建的自定义项，或者发布不断扩充的 [Azure 市场项列表](azure-stack-marketplace-azure-items.md)中所列的项。 将某个项发布到市场后，用户在五分钟内就能看到它。

> [!CAUTION]  
> 将所有库项项目（包括映像和 JSON 文件）发布到 Azure Stack 市场后，无需身份验证即可访问这些项目。 有关发布自定义市场项时的其他注意事项，请参阅[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)。

若要打开市场，请在管理员门户中选择“+ 创建资源”  。

![市场](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>市场项

Azure Stack 市场项是用户可下载并使用的服务、应用程序或资源。 所有用户都可以看到所有 Azure Stack 市场项，包括计划和套餐等管理项。 无需订阅即可查看这些项，但对于用户而言，这些项不具有功能性。

每个市场项具有：

* 用于资源预配的 Azure 资源管理器模板。
* 元数据，例如字符串、图标和其他营销材料。
* 有关在门户中显示该项的格式信息。

发布到市场的每个项使用 Azure 库包 (.azpkg) 格式。 请单独地将部署或运行时资源（代码、包含软件的 .zip 文件或虚拟机映像）添加到 Azure Stack，而不要将其作为市场项的一部分添加。

在 1803 和更高版本中，从 Azure 下载映像或上传自定义映像时，Azure Stack 会将映像转换为稀疏文件。 此过程会在添加映像时延长时间，但可节省空间并加速这些映像的部署。 转换仅适用于新映像。 现有映像不会更改。

## <a name="next-steps"></a>后续步骤

* [下载市场项](azure-stack-download-azure-marketplace-item.md)  
* [创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)

<!-- Update_Description: wording update -->
