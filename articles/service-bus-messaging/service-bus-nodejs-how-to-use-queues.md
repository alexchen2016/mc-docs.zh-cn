---
title: 如何通过 Node.js 使用服务总线队列 | Azure
description: 了解如何在来自 Node.js 应用程序的 Azure 中使用服务总线队列。
services: service-bus-messaging
documentationcenter: nodejs
author: lingliw
manager: digimobile
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: v-lingwu
ms.openlocfilehash: b80bb4cf9ba6d01462e7c4f38ba99ee87dd9c353
ms.sourcegitcommit: 4c10e625a71a955a0de69e9b2d10a61cac6fcb06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67046969"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>如何通过 Node.js 使用服务总线队列

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本教程介绍如何创建 Node.js 应用程序，以便向服务总线队列发送消息以及从中接收消息。 示例用 JavaScript 编写并使用 Node.js Azure 模块。 

## <a name="prerequisites"></a>先决条件
1. Azure 订阅。 若要完成本教程，需要一个 Azure 帐户。 可以激活 [MSDN 订阅者权益](https://www.azure.cn/zh-cn/support/legal/offer-rate-plans/)或注册[试用帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)。
2. 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。
    1. 阅读服务总线**队列**的快速**概述**。 
    2. 创建一个服务总线**命名空间**。 
    3. 获取**连接字符串**。 

        > [!NOTE]
        > 在本教程中，需使用 Node.js 在服务总线命名空间中创建一个**队列**。 
 

## <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序
创建一个空的 Node.js 应用程序。 有关如何创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站][Create and deploy a Node.js application to an Azure Website]或 [Node.js 云服务][Node.js Cloud Service]（使用 Windows PowerShell）。

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
若要使用 Azure 服务总线，请下载并使用 Node.js Azure 包。 此程序包包括一组用来与服务总线 REST 服务通信的库。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node 包管理器 (NPM) 可获取该程序包
1. 使用 **Windows PowerShell for Node.js** 命令窗口导航到在其中创建了示例应用程序的 **c:\\node\\sbqueues\\WebRole1** 文件夹。
2. 在命令窗口中键入 **npm install azure**，这应该产生类似如下的输出：

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. 可以手动运行 **ls** 命令来验证是否创建了 **node_modules** 文件夹。 在该文件夹中，找到 **azure** 包，其中包含访问服务总线队列所需的库。

### <a name="import-the-module"></a>导入模块
使用记事本或其他文本编辑器将以下内容添加到应用程序的 **server.js** 文件的顶部：

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>设置 Azure 服务总线连接
Azure 模块读取环境变量 `AZURE_SERVICEBUS_CONNECTION_STRING`，获取连接到服务总线所需的信息。 如果未设置此环境变量，则在调用 `createServiceBusService` 时必须指定帐户信息。

有关在 [Azure 门户][Azure portal]中为 Azure 网站设置环境变量的示例，请参阅[使用存储的 Node.js Web 应用程序][Node.js Web Application with Storage]。

## <a name="create-a-queue"></a>创建队列
可以通过 **ServiceBusService** 对象处理服务总线队列。 以下代码创建 **ServiceBusService** 对象。 将它添加到靠近 **server.js** 文件顶部、用于导入 Azure 模块的语句之后的位置：

```javascript
var serviceBusService = azure.createServiceBusService();
```

通过对 ServiceBusService 对象调用 `createQueueIfNotExists`，会返回指定的队列（如果存在），否则会使用指定的名称创建一个新队列  。 以下代码使用 `createQueueIfNotExists` 创建或连接到名为 `myqueue` 的队列：

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService` 方法还支持其他选项，通过这些选项可以重写默认队列设置，例如消息生存时间或最大队列大小。 以下示例将最大队列大小设置为 5 GB，将生存时间 (TTL) 值设置为 1 分钟：

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>筛选器
可选的筛选操作可应用于使用 **ServiceBusService** 执行的操作。 筛选操作可包括日志记录、自动重试等。筛选器是实现具有签名的方法的对象：

```javascript
function handle (requestOptions, next)
```

在对请求选项执行预处理后，该方法必须调用 `next` 并传递具有以下签名的回调：

```javascript
function (returnObject, finalCallback, next)
```

在此回调中并且在处理 `returnObject`（来自对服务器请求的响应）后，回调必须调用 `next`（如果存在），继续处理其他筛选器，或者调用 `finalCallback`，结束服务调用。

用于 Node.js 的 Azure SDK 中包含实现重试逻辑的两个筛选器：`ExponentialRetryPolicyFilter` 和 `LinearRetryPolicyFilter`。 以下代码创建使用 `ExponentialRetryPolicyFilter` 的 `ServiceBusService` 对象：

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
要向服务总线队列发送消息，应用程序需对 ServiceBusService 对象调用 `sendQueueMessage` 方法  。 发往服务总线队列的消息以及从服务总线队列接收的消息是 **BrokeredMessage** 对象，它们具有一组标准属性（如 **Label** 和 **TimeToLive**）、一个用来保存自定义应用程序特定属性的字典和一段任意应用程序数据正文。 应用程序可以通过将字符串作为消息传递来设置消息正文。 任何必需的标准属性将用默认值来填充。

以下示例演示如何使用 `sendQueueMessage` 向名为 `myqueue` 的队列发送一条测试消息：

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 一个队列可包含的消息数不受限制，但消息的总大小受限。 此队列大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额][Service Bus quotas]。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
对 ServiceBusService 对象使用 `receiveQueueMessage` 方法可从队列接收消息  。 默认情况下，消息被读取后即从队列删除；但是可以读取（速览）并锁定消息而不将其从队列删除，只要将可选参数 `isPeekLock` 设置为“true”即可  。

在接收过程中读取并删除消息的默认行为是最简单的模式，并且最适合在发生故障时应用程序可以容忍不处理消息的情况。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“已使用”，因此当应用程序重启并重新开始使用消息时，它会遗漏在发生崩溃前使用的消息。

如果将 `isPeekLock` 参数设置为“true”，则接收会变成一个两阶段操作，从而可支持无法容忍遗漏消息的应用程序  。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，并将该消息返回到应用程序。 应用程序处理完该消息（或将它可靠地存储起来留待将来处理）后，通过调用 `deleteMessage` 方法并提供要删除的消息作为参数，完成接收过程的第二阶段。 `deleteMessage` 方法会将消息标记为已使用，并将其从队列中删除。

以下示例演示如何使用 `receiveQueueMessage` 接收和处理消息。 该示例先接收并删除一条消息，然后使用设置为“true”的 `isPeekLock` 接收一条消息，最后使用 `deleteMessage` 删除该消息  ：

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息
Service Bus 提供了相关功能来帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则它可以对 ServiceBusService 对象调用 `unlockMessage` 方法  。 这会导致 Service Bus 解锁队列中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

还存在与队列中已锁定的消息相关联的超时，并且如果应用程序未能在锁定超时到期之前处理消息（例如，如果应用程序崩溃），服务总线则将自动解锁该消息，使它可以再次被接收。

如果应用程序在处理消息之后，但在调用 `deleteMessage` 方法之前崩溃，则在应用程序重启时会将该消息重新传送给它。 此情况通常称作*至少处理一次*，即每条消息至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 这通常可以通过消息的 **MessageId** 属性来实现，该属性在多次传送尝试中保持不变。

## <a name="next-steps"></a>后续步骤
若要了解有关队列的详细信息，请参阅以下资源。

* [队列、主题和订阅][Queues, topics, and subscriptions]
* GitHub 上的 [Azure SDK for Node][Azure SDK for Node] 存储库
* [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)

  [Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.cn

  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Queues, topics, and subscriptions]: ./service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
  [Service Bus quotas]: ./service-bus-quotas.md