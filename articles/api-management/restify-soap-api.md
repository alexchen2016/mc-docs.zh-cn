---
title: 使用 Azure 门户导入 SOAP API 并将其转换为 REST
description: 了解如何使用 API 管理导入 SOAP API 并将其转换为 REST。
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
origin.date: 11/22/2017
ms.date: 02/26/2018
ms.author: apimpm
ms.openlocfilehash: c7832e08508fe0c6d0adb9cda0de32ad0422944b
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52652723"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>导入 SOAP API 并将其转换为 REST

本文介绍如何导入 SOAP API 并将其转换为 REST。 此外，介绍如何测试 APIM API。

在本文中，学习如何：

> [!div class="checklist"]
> * 导入 SOAP API 并将其转换为 REST
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>导入和发布后端 API

1. 在“API 管理”下面选择“API”。
2. 从“添加新的 API”列表中选择“WSDL”。

    ![SOAP API](./media/restify-soap-api/wsdl-api.png)
    
3. 在“WSDL 规范”，输入要将 SOAP API 放到的 URL。
4. 单击“SOAP 到 REST”单选按钮。 单击此选项后，APIM 会尝试在 XML 与 JSON 之间进行自动转换。 在这种情况下，使用者应以 Restful API 的形式调用该 API，这会返回 JSON API。 APIM 将每个请求转换为 SOAP 调用。

    ![SOAP 到 REST](./media/restify-soap-api/soap-to-rest.png)

5. 按 Tab 键。

    以下字段中填充了来自 SOAP API 的信息：显示名称、名称、说明。
6. 添加 API URL 后缀。 后缀是用于在该 APIM 实例中标识此特定 API 的名称。 它在此 APIM 实例中必须唯一。
9. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品。  如果想要发布 API 并使其对开发人员可用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。

    产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。

    默认情况下，每个 API 管理实例附带两个示例产品：

    * **入门**
    * **不受限制**   
10. 选择“创建” 。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 APIM API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。  

1. 选择上一步中创建的 API。
2. 按“测试”选项卡。
3. 选择某个操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。 
1. 按“发送”。

    后端以“200 正常”和某些数据做出响应。

## <a name="call-operation"></a>从开发人员门户调用操作

此外，也可从“开发人员门户”调用操作来测试 API。 

1. 选择在“导入和发布后端 API”步骤中创建的 API。
2. 按“开发人员门户”。

    “开发人员门户”站点随即打开。
3. 选择创建的 **API**。
4. 单击要测试的操作。
5. 按“试用”。
6. 按“发送”。
    
    调用操作后，开发人员门户会显示**响应状态**、**响应标头**以及任何**响应内容**。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)