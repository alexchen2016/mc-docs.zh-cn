---
title: 连接到 SAP 系统 - Azure 逻辑应用 | Microsoft Docs
description: 如何使用 Azure 逻辑应用将工作流自动化，以访问和管理 SAP 资源
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
manager: jeconnoc
ms.author: v-yiso
origin.date: 09/14/2018
ms.topic: article
ms.date: 12/10/2018
ms.reviewer: klam, divswa, LADocs
tags: connectors
ms.openlocfilehash: d71dac39dc53d16d6433939d811b831404fad0d5
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675074"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 SAP 系统

本文介绍如何使用 SAP ERP Central Component (ECC) 连接器在逻辑应用内部访问本地 SAP 资源。 SAP ECC 连接器支持通过中间文档 (IDoc)、业务应用程序编程接口 (BAPI) 或远程函数调用 (RFC) 来与基于 SAP Netweaver 的系统相互进行消息或数据集成。

SAP ECC 连接器使用 <a href="https://support.sap.com/en/product/connectors/msnet.html">SAP .Net 连接器 (NCo) 库</a>，并提供以下操作：

- **发送到 SAP**：在 SAP 系统中通过 tRFC 发送 IDoc 或调用 BAPI 函数。
- **从 SAP 接收**：通过 tRFC 从 SAP 系统接收 IDoc 或 BAPI 函数调用。
- **生成架构**：为 IDoc、BAPI 或 RFC 生成 SAP 项目的架构。

SAP 连接器通过[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)与本地 SAP 系统集成。 例如，在“发送”方案中，如果将消息从逻辑应用发送到 SAP 系统，则数据网关将充当 RFC 客户端，将从逻辑应用收到的请求转发到 SAP。
同理，在“接收”方案中，数据网关充当 RFC 服务器，从 SAP 接收请求并将其转发到逻辑应用。 

本文介绍如何创建与 SAP 集成的示例逻辑应用，并阐述以前已介绍过的集成方案。

## <a name="prerequisites"></a>先决条件

若要按本文中的步骤操作，需准备好以下各项：

* Azure 订阅。 如果还没有 Azure 订阅，请<a href="https://www.azure.cn/pricing/1rmb-trial/" target="_blank">注册一个 Azure 试用帐户</a>。

* 要从中访问 SAP 系统的逻辑应用，以及用于启动逻辑应用工作流的触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP 应用程序服务器</a>或 <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP 消息服务器</a>

* 在任何本地计算机上下载并安装最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)。 在继续操作之前，请确保在 Azure 门户中设置网关。 网关有助于安全访问数据，资源位于本地。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 在本地数据网关所安装到的同一台计算机上，下载并安装最新的 SAP 客户端库，目前为<a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">适用于 Microsoft .NET Framework 4.0 和 Windows 64 位 (x64) 的 SAP 连接器 (NCo) 3.0.21.0</a>。 安装此版本或更高版本的原因是：

  * 如果同时发送多个 IDoc 消息，早期的 SAP NCo 版本可能死锁。 
  这种状态会阻止向 SAP 目标发送所有后续消息，从而导致消息超时。

  * 本地数据网关只能在 64 位系统上运行。 
  否则，会收到“错误的映像”错误，因为数据网关主机服务不支持 32 位程序集。

  * 数据网关主机服务和 Microsoft SAP 适配器使用 .NET Framework 4.5。 SAP NCo for .NET Framework 4.0 适用于使用 .NET 运行时 4.0 到 4.7.1 的进程。 
  SAP NCo for .NET Framework 2.0 适用于使用 .NET 运行时 2.0 到 3.5 的进程，而不再适用于最新的本地数据网关。

* 可发送到 SAP 服务器的消息内容，例如示例 IDoc 文件。 此内容必须采用 XML 格式，并且包含要使用的 SAP 操作的命名空间。

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>发送到 SAP

本示例使用可通过 HTTP 请求触发的逻辑应用。 该逻辑应用将一个中间文档 (IDoc) 发送到 SAP 服务器，并向调用逻辑应用的请求方返回响应。 

### <a name="add-http-request-trigger"></a>添加 HTTP 请求触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

本示例创建一个包含 Azure 中的终结点的逻辑应用，方便将 HTTP POST 请求发送到逻辑应用。 当逻辑应用程序收到这些 HTTP 请求时，触发器将会触发，并运行工作流中的下一个步骤。

1. 在 [Azure 门户](https://portal.azure.cn)中创建一个空白的逻辑应用。此时会打开逻辑应用设计器。 

2. 在搜索框中，输入“http 请求”作为筛选器。 在触发器列表中选择此触发器：“请求 - 当收到 HTTP 请求时”

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-trigger.png)

3. 现在请保存逻辑应用，以便为逻辑应用生成终结点 URL。
在设计器工具栏上，选择“保存”。 

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>添加 SAP 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 如果尚未将触发器添加到逻辑应用，但需要遵循本示例，请[添加此部分所述的触发器](#add-trigger)。

1. 在逻辑应用设计器的触发器下，选择“新建步骤” > “添加操作”。

   ![添加操作](./media/logic-apps-using-sap-connector/add-action.png) 

2. 在搜索框中，输入“sap”作为筛选器。 在操作列表中选择此操作：“将消息发送到 SAP”
  
   ![选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   如果不执行搜索，可以选择“企业”选项卡，然后选择 SAP 操作。

   ![在“企业”选项卡中选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 

   **创建本地 SAP 连接**

   1. 提供 SAP 服务器的连接信息。 
   对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。

      如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

      ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）： 

      ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 完成后，选择“创建”。 
   
      逻辑应用会设置并测试连接，确保连接正常工作。

4. 现在，请找到并选择 SAP 服务器中的某个操作。 

   1. 在“SAP 操作”框中，选择文件夹图标。 
   在文件列表中，找到并选择要使用的 SAP 消息。 
   使用箭头可在列表中导航。

      此示例选择了类型为“订单”的 IDoc。 

      ![找到并选择 IDoc 操作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到所需的操作，可以手动输入路径，例如：

      ![手动提供 IDoc 操作的路径](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 通过表达式编辑器提供 SAP 操作的值。 这样，便可以对不同的消息类型使用相同的操作。

      有关 IDoc 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   2. 在“输入消息”框中单击，以显示动态内容列表。 
   在该列表中的“收到 HTTP 请求时”下面，选择“正文”字段。 

      此步骤包含来自 HTTP 请求触发器的正文内容，并将该输出发送到 SAP 服务器。

      ![选择“正文”字段](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成后，SAP 操作如以下示例所示：

      ![完成 SAP 操作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

<a name="add-response"></a>

### <a name="add-http-response-action"></a>添加 HTTP 响应操作

现在，请将响应操作添加到逻辑应用的工作流，并包括来自 SAP 操作的输出。 这样，逻辑应用便可将来自 SAP 服务器的结果返回到原始请求方。 

1. 在逻辑应用设计器中的 SAP 操作下，选择“新建步骤” > “添加操作”。

2. 在搜索框中，输入“响应”作为筛选器。 在操作列表中选择此操作：“请求 - 响应”

3. 在“正文”框中单击，以显示动态内容列表。 在该列表中的“发送到 SAP”下面，选择“正文”字段。 

   ![完成 SAP 操作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. 保存逻辑应用。 

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概览”。 在工具栏上选择“启用”。 

2. 在逻辑应用设计器工具栏上，选择“运行”。 此步骤会手动启动逻辑应用。

3. 通过将 HTTP POST 请求发送到 HTTP 请求触发器中的 URL 来触发逻辑应用，并在请求中包括消息内容。 若要发送请求，可以使用 [Postman](https://www.getpostman.com/apps) 等工具。 

   在本文中，请求会发送 IDoc 文件，该文件必须采用 XML 格式，并且包含你所用 SAP 操作的命名空间，例如： 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. 发送 HTTP 请求之后，等待逻辑应用返回响应。

> [!NOTE]
> 如果未在[请求超时限制](./logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用可能会超时。 如果发生这种情况，请求可能会被阻止。 为了诊断问题，请了解如何[检查和监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

祝贺你，现已创建一个可与 SAP 服务器通信的逻辑应用。 为逻辑应用设置 SAP 连接后，可以探索其他可用的 SAP 操作，例如 BAPI 和 RFC。

## <a name="receive-from-sap"></a>从 SAP 接收

此示例使用从 SAP 系统收到消息时触发的逻辑应用。 

### <a name="add-sap-trigger"></a>添加 SAP 触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 

2. 在搜索框中，输入“sap”作为筛选器。 在触发器列表中选择此触发器：“从 SAP 收到消息时”

   ![添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   或者，可以转到“企业”选项卡并选择触发器

   ![在“企业”选项卡中添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 

   **创建本地 SAP 连接**

   1. 提供 SAP 服务器的连接信息。 
   对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。

      如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

      ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）：

      ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. 根据 SAP 系统配置提供所需的参数。 

   可以选择性地提供一个或多个 SAP 操作。 
   此操作列表指定触发器通过数据网关从 SAP 服务器接收的消息。 
   空列表指定触发器接收所有消息。 
   如果列表中不包含多条消息，则触发器只会接收列表中指定的消息。 网关会拒绝从 SAP 服务器发送的其他任何消息。

   可以通过文件选取器选择 SAP 操作：

   ![选择 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，可以手动指定操作：

   ![手动输入 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   以下示例演示将触发器设置为接收多个消息时操作的显示方式。

   ![触发器示例](media/logic-apps-using-sap-connector/example-trigger.png)  

   有关 SAP 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

5. 现在请保存逻辑应用，以便可以开始从 SAP 系统接收消息。
在设计器工具栏上，选择“保存”。 

现在，逻辑应用已准备好从 SAP 系统接收消息。 

> [!NOTE]
> SAP 触发器不是轮询触发器，而是基于 Webhook 的触发器。 仅当存在消息时，才会从网关调用触发器，因此无需轮询。 

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 若要触发逻辑应用，请从 SAP 系统发送一条消息。

2. 在逻辑应用菜单中选择“概述”，然后查看逻辑应用的任何新运行的“运行历史记录”。 

3. 打开最近的运行，触发器输出部分会显示从 SAP 系统发送的消息。

## <a name="generate-schemas-for-artifacts-in-sap"></a>为 SAP 中的项目生成架构

本示例使用可通过 HTTP 请求触发的逻辑应用。 SAP 操作将请求发送到 SAP 系统，以便为指定的中间文档 (IDoc) 和 BAPI 生成架构。 使用 Azure 资源管理器连接器将响应中返回的架构上传到集成帐户。

### <a name="add-http-request-trigger"></a>添加 HTTP 请求触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 

2. 在搜索框中，输入“http 请求”作为筛选器。 在触发器列表中选择此触发器：“请求 - 当收到 HTTP 请求时”

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-trigger.png)

3. 现在请保存逻辑应用，以便为逻辑应用生成终结点 URL。
在设计器工具栏上，选择“保存”。 

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>添加 SAP 操作以生成架构

1. 在逻辑应用设计器的触发器下，选择“新建步骤” > “添加操作”。

   ![添加操作](./media/logic-apps-using-sap-connector/add-action.png) 

2. 在搜索框中，输入“sap”作为筛选器。 在操作列表中选择此操作：“生成架构”
  
   ![选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，也可以选择“企业”选项卡，然后选择 SAP 操作。

   ![在“企业”选项卡中选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 

   **创建本地 SAP 连接**

   1. 提供 SAP 服务器的连接信息。 
   对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。

      如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

      ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）：
   
      ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 完成后，选择“创建”。 逻辑应用会设置并测试连接，确保连接正常工作。

4. 提供要为其生成架构的项目的路径。

   可以通过文件选取器选择 SAP 操作：

   ![选择 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，可以手动输入操作：

   ![手动输入 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   若要为多个项目生成架构，请提供每个项目的 SAP 操作详细信息，例如：

   ![选择“添加新项”](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![显示两个项](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   有关 SAP 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

5. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 在设计器工具栏上，选择“运行”以触发逻辑应用的运行。

2. 打开该运行，并检查“生成架构”操作的输出。 

   输出中会显示针对指定的消息列表生成的架构。

### <a name="upload-schemas-to-integration-account"></a>将架构上传到集成帐户

（可选）可以下载生成的架构，或将其存储在 Blob、存储或集成帐户等存储库中。 集成帐户为其他 XML 操作提供一流的体验。本示例演示如何使用 Azure 资源管理器连接器将架构上传到同一逻辑应用的集成帐户。

有关连接器 Swagger 文件描述的连接器的技术详细信息，请查看以下参考文章： 

* [SAP 应用程序服务器](/connectors/sapapplicationserver/)
* [SAP 消息服务器](/connectors/sapmessageserver/)

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 从逻辑应用[连接到本地系统](../logic-apps/logic-apps-gateway-connection.md)
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)