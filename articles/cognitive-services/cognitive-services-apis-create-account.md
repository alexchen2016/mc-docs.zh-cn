---
title: 在 Azure 门户中创建认知服务帐户
titlesuffix: Azure Cognitive Services
description: 如何在 Azure 门户中创建 Azure 认知服务 API 帐户。
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
origin.date: 03/26/2019
ms.date: 04/23/2019
ms.author: v-junlch
ms.openlocfilehash: 6a531705951c6a9c6044af9b760f4a0f851602b2
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854894"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建认知服务帐户

本快速入门介绍如何注册 Azure 认知服务并创建单服务或多服务订阅。 这些服务由 Azure [资源](/azure-resource-manager/resource-group-portal)表示，允许连接到一个或多个 Azure 认知服务 API。

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 订阅。 [创建帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>创建并订阅 Azure 认知服务资源

开始之前，请务必了解有两种类型的 Azure 认知服务订阅。 第一个是单服务订阅，例如计算机视觉或语音服务。 单服务订阅仅限于该资源。 第二个是 Azure 认知服务的多服务订阅。 此订阅允许将一个订阅用于大多数 Azure 认知服务。 此选项还可整合帐单。 请参阅[认知服务定价](https://www.azure.cn/pricing/details/cognitive-services/)了解其他信息。

以下部分将引导创建单服务或多服务订阅。


### <a name="multi-service-subscription"></a>多服务订阅

1. 登录 [Azure 门户](https://portal.azure.cn)，然后单击“创建资源”。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 找到搜索栏，然后输入：“认知服务”。

    ![搜索“认知服务”](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. 选择“认知服务”。

    ![选择“认知服务”](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. 在“创建”页中提供以下信息： 

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 建议使用描述性的名称，例如“MyCognitiveServicesAccount”。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://www.azure.cn/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>单服务订阅

1. 登录 [Azure 门户](https://portal.azure.cn)，然后单击“创建资源”。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. 在 Azure 市场下，选择“AI + 机器学习”。 如果没有看到感兴趣的服务，请单击“查看全部”，以查看认知服务 API 的完整目录。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在“创建”页中提供以下信息： 

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 建议使用描述性的名称，例如“MyNameFaceAPIAccount”。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://www.azure.cn/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>访问资源

> [!NOTE]
> 订阅所有者可以通过应用 [Azure 策略](/governance/policy/overview#policy-definition)，分配“不允许的资源类型”策略定义并指定“Microsoft.CognitiveServices/accounts”作为目标资源类型来禁止为资源组和订阅创建认知服务帐户。

创建资源后，如果已固定该资源，则可以从 Azure 仪表板对其进行访问。 否则，可以在“资源组”中查找该资源。

在认知服务资源中，可以使用“概述”部分中的终结点 URL 和密钥在应用程序中进行 API 调用。

![“资源”屏幕](media/cognitive-services-apis-create-account/resourceScreen.png)


## <a name="see-also"></a>另请参阅

* [快速入门：从图像中提取手写文本](/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [教程：创建一个用于检测和定格图像中人脸的应用](/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)

<!-- Update_Description: wording update -->
