---
title: 了解如何在逻辑应用中使用 SharePoint Online 连接器
description: 使用 SharePoint Online 连接器创建逻辑应用以管理 SharePoint 上的列表。
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: ''
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
origin.date: 07/19/2016
ms.author: v-yiso
ms.date: 03/26/2018
ms.openlocfilehash: b0762c3592e85d71314dfea0abf40ffc167b7796
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52649649"
---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 连接器入门
使用 SharePoint Online 连接器管理 SharePoint 列表。  

若要使用[任何连接器](apis-list.md)，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md) 开始操作。

## <a name="connect-to-sharepoint-online"></a>连接到 SharePoint Online
在逻辑应用访问任何服务之前，必须先创建到该服务的*连接*。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-sharepoint-online"></a>创建到 SharePoint Online 的连接
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]


## <a name="use-a-sharepoint-online-trigger"></a>使用 SharePoint Online 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]


## <a name="use-a-sharepoint-online-action"></a>使用 SharePoint Online 操作
操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]


## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/sharepoint/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)

