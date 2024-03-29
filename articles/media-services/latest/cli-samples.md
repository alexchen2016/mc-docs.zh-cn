---
title: Azure CLI 示例 - Azure 媒体服务 | Azure
description: Azure 媒体服务的 Azure CLI 示例
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
origin.date: 04/15/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.openlocfilehash: d0540b54ffb8f72c7f597adb6455e488ee26ac13
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52656206"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure 媒体服务的 Azure CLI 示例

下表包括 Azure 媒体服务的 Azure CLI 示例的链接。

|  |  |
|---|---|
|**帐户**||
| [创建媒体服务帐户](./scripts/cli-create-account.md) | 创建 Azure 媒体服务帐户。 此外，还需创建可用于访问 API 的服务主体，以便以编程方式管理帐户。 |
| [重置帐户凭据](./scripts/cli-reset-account-credentials.md)|重置帐户凭据和恢复 app.config 设置。|
|**资产**||
| [创建资产](./scripts/cli-create-asset.md)|创建要将内容上传到其中的媒体服务资产。|
| [上传文件](./scripts/cli-upload-file-asset.md)|将本地文件上传到存储容器。|
| [发布资产](./scripts/cli-publish-asset.md)| 创建流式处理定位符并取回流式处理 URL。 |
| **转换**和**作业**||
| [创建转换](./scripts/cli-create-transform.md)|演示如何创建转换。 转换描述了处理视频或音频文件的任务的简单工作流（通常称为“工作程序”）。<br/> 应始终检查具有所需名称和“工作程序”的转换是否已存在。 如果已存在，请重用该转换。 |
| [创建作业](./scripts/cli-create-jobs.md)|使用 HTTPs URL 将作业提交到简单编码转换。|

