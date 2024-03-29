---
title: Application Insights，Node.js
titleSuffix: Azure Cognitive Services
description: 使用 Node.js 生成与 LUIS 应用程序和 Application Insights 集成的机器人。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 02e7bfe8842e267cf3c16b3a89a5d45704ec3b2a
ms.sourcegitcommit: e77582e79df32272e64c6765fdb3613241671c20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67135983"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>将 LUIS 结果添加到 Application Insights 和 Azure 函数
本教程将 LUIS 请求和响应信息添加到 [Application Insights](https://www.azure.cn/services/application-insights/) 遥测数据存储。 添加该数据后，可使用 Kusto 语言进行查询，或使用 PowerBi 对陈述的意向和实体进行实时分析、聚合和报告。 此分析有助于确定是否应添加或编辑 LUIS 应用的意向和实体。

该机器人是使用 Bot Framework 3.x 和 Azure Web 应用机器人生成的。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将 Application Insights 库添加到 Web 应用机器人
> * 捕获 LUIS 查询结果并发送给 Application Insights
> * 查询 Application Insights，获取首要意向、分数和表述

## <a name="prerequisites"></a>先决条件

* 使用[上一教程](luis-nodejs-tutorial-build-bot-framework-sample.md)中已启用 Application Insights 的 LUIS Web 应用机器人  。 

> [!Tip]
> 如果还没有订阅，可以注册一个[试用帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)。

本教程中的所有代码均可在 [Azure 示例 GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs)中找到，并且与本教程关联的每行均注释有 `//APPINSIGHT:`。 

## <a name="web-app-bot-with-luis"></a>Web 应用机器人与 LUIS
本教程假定你有如下代码或已完成[其他教程](luis-nodejs-tutorial-build-bot-framework-sample.md)： 

```
    /*-----------------------------------------------------------------------------
    This template demonstrates how to use dialogs with a LuisRecognizer to add 
    natural language support to a bot. 
    For a complete walkthrough of creating this type of bot see the article at
    https://aka.ms/abs-node-luis
    -----------------------------------------------------------------------------*/

    var restify = require('restify');
    var builder = require('botbuilder');
    var botbuilder_azure = require("botbuilder-azure");

    // Setup Restify Server
    var server = restify.createServer();
    server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log('%s listening to %s', server.name, server.url); 
    });
    
    // Create chat connector for communicating with the Bot Framework Service
    var connector = new builder.ChatConnector({
        appId: process.env.MicrosoftAppId,
        appPassword: process.env.MicrosoftAppPassword,
        openIdMetadata: process.env.BotOpenIdMetadata 
    });

    // Listen for messages from users 
    server.post('/api/messages', connector.listen());

    /*----------------------------------------------------------------------------------------
    * Bot Storage: This is a great spot to register the private state storage for your bot. 
    * We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
    * For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
    * ---------------------------------------------------------------------------------------- */

    var tableName = 'botdata';
    var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
    var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

    // Create your bot with a function to receive messages from the user
    // This default message handler is invoked if the user's utterance doesn't
    // match any intents handled by other dialogs.
    var bot = new builder.UniversalBot(connector, function (session, args) {
        session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
    });

    bot.set('storage', tableStorage);

    // Make sure you add code to validate these fields
    var luisAppId = process.env.LuisAppId;
    var luisAPIKey = process.env.LuisAPIKey;
    var luisAPIHostName = process.env.LuisAPIHostName || 'chinaeast2.api.cognitive.azure.cn';

    const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

    // Create a recognizer that gets intents from LUIS, and add it to the bot
    var recognizer = new builder.LuisRecognizer(LuisModelUrl);
    bot.recognizer(recognizer);

    // Add a dialog for each intent that the LUIS app recognizes.
    // See https://docs.microsoft.com/en-us/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
    bot.dialog('TurnOnDialog',
        (session, args) => {
            // Resolve and store any HomeAutomation.Device entity passed from LUIS.
            var intent = args.intent;
            var device = builder.EntityRecognizer.findEntity(intent.entities, 'HomeAutomation.Device');

            // Turn on a specific device if a device entity is detected by LUIS
            if (device) {
                session.send('Ok, turning on the %s.', device.entity);
                // Put your code here for calling the IoT web service that turns on a device
            } else {
                // Assuming turning on lights is the default
                session.send('Ok, turning on the lights');
                // Put your code here for calling the IoT web service that turns on a device
            }
            session.endDialog();
        }
    ).triggerAction({
        matches: 'HomeAutomation.TurnOn'
    })

    bot.dialog('TurnOffDialog',
        (session, args) => {
            // Resolve and store any HomeAutomation.Device entity passed from LUIS.
            var intent = args.intent;
            var device = builder.EntityRecognizer.findEntity(intent.entities, 'HomeAutomation.Device');

            // Turn off a specific device if a device entity is detected by LUIS
            if (device) {
                session.send('Ok, turning off the %s.', device.entity);
                // Put your code here for calling the IoT web service that turns off a device
            } else {
                // Assuming turning off lights is the default
                session.send('Ok, turning off the lights.');
                // Put your code here for calling the IoT web service that turns off a device
            }
            session.endDialog();
        }
    ).triggerAction({
        matches: 'HomeAutomation.TurnOff'
    })
```

## <a name="add-application-insights-library-to-web-app-bot"></a>将 Application Insights 库添加到 Web 应用机器人
目前，此 Web 应用机器人中使用的 Application Insights 服务收集机器人的常规状态遥测数据。 它不会收集在检查和修复意向与实体时所需的 LUIS 请求和响应信息。 

若要捕获 LUIS 请求和响应，需在 Web 应用机器人中安装 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 包，并在 **app.js** 文件中配置该包。 然后，意向对话处理程序需将 LUIS 请求和响应信息发送到 Application Insights。 

1. 在 Azure 门户上的 Web 应用机器人服务中，选择“机器人管理”部分下的“生成”。   

    ![在 Azure 门户上的 Web 应用机器人服务中，选择“机器人管理”部分下的“生成”。](./media/luis-tutorial-appinsights/build.png)

2. 此时会打开一个包含“应用服务编辑器”的新浏览器标签页。 在顶部栏中选择应用名称，然后选择“打开 Kudu 控制台”。  

    ![在顶部栏中选择应用名称，然后选择“打开 Kudu 控制台”。](./media/luis-tutorial-appinsights/kudu-console.png)

3. 在控制台中输入以下命令，安装 Application Insights 和 Underscore 包：

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![使用 npm 命令安装 Application Insights 和 Underscore 包](./media/luis-tutorial-appinsights/npm-install.png)

    等待这些包安装完成：

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Kudu 控制台浏览器标签页中的操作到此完成。

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>捕获 LUIS 查询结果并发送给 Application Insights
1. 在“应用服务编辑器”浏览器选项卡中，打开 **app.js** 文件。

2. 在现有的 `requires` 行下添加以下 NPM 库：

```
    // APPINSIGHT: Add underscore for flattening to name/value pairs
    var _ = require("underscore");

    // APPINSIGHT: Add NPM package applicaitoninsights
    let appInsights = require("applicationinsights");
```

3. 创建 Application Insights 对象，并使用 Web 应用机器人应用程序设置 **BotDevInsightsKey**： 

```
    // APPINSIGHT: Set up ApplicationInsights with Web App Bot settings "BotDevAppInsightsKey"
    appInsights.setup(process.env.BotDevAppInsightsKey)
        .setAutoDependencyCorrelation(true)
        .setAutoCollectRequests(true)
        .setAutoCollectPerformance(true)
        .setAutoCollectExceptions(true)
        .setAutoCollectDependencies(true)
        .setAutoCollectConsole(true,true)
        .setUseDiskRetryCaching(true)
        .start();

    // APPINSIGHT: Get client 
    let appInsightsClient = appInsights.defaultClient;
```

4. 添加 **appInsightsLog** 函数：

```
    // APPINSIGHT: Log LUIS results to Application Insights
    // APPINSIGHT: must flatten as name/value pairs
    var appInsightsLog = function(session,args) {
        
        // APPINSIGHT: put bot session and LUIS results into single object
        var data = Object.assign({}, session.message,args);
        
        // APPINSIGHT: ApplicationInsights Trace 
        console.log(data);

        // APPINSIGHT: Flatten data into name/value pairs
        flatten = function(x, result, prefix) {
            if(_.isObject(x)) {
                _.each(x, function(v, k) {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }

        // APPINSIGHT: call fn to flatten data
        var flattenedData = flatten(data, {})

        // APPINSIGHT: send data to Application Insights
        appInsightsClient.trackEvent({name: "LUIS-results", properties: flattenedData});
    }
```

    The last line of the function is where the data is added to Application Insights. The event's name is **LUIS-results**, a unique name apart from any other telemetry data collected by this web app bot. 

5. 使用 **appInsightsLog** 函数。 在每个意向对话框中添加该函数：

```
    // APPINSIGHT: Log results to Application Insights
    appInsightsLog(session,args);
```

6. 要测试 Web 应用机器人，请使用“通过网上聊天执行测试”功能  。 由于所有工作在 Application Insights 中发生，而不是在机器人响应中发生，因此，不会出现任何差异。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中查看 LUIS 条目
打开 Application Insights 以查看 LUIS 条目。 

1. 在门户中，选择“所有资源”，然后按 Web 应用机器人的名称进行筛选  。 单击“Application Insights”类型的资源  。 Application Insights 的图标是灯泡。 

    ![[在 Azure 门户中搜索 app insights](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. 资源打开后，单击最右侧面板中的“搜索”图标（放大镜）  。 右侧将显示一个新面板。 该面板可能需要一秒钟才能显示出来，具体取决于找到的遥测数据量。 搜索 `LUIS-results` 并按 Enter 键。 该列表已缩减为仅限本教程添加的 LUIS 查询结果。

    ![筛选依赖项](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. 选择最上面的条目。 一个新窗口将显示更详细的数据，包括在最右侧显示 LUIS 查询的自定义数据。 该数据包括首要意向及其评分。

    ![依赖项详细信息](./media/luis-tutorial-appinsights/app-insights-detail.png)

    完成后，选择最右上角的“X”，返回依赖项列表  。 


> [!Tip]
> 如果想要保存依赖项列表并稍后回查看，请依次单击“...更多”>“保存收藏”   。

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查询 Application Insights，获取意向、评分和陈述
Application Insights 支持查询使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 语言的数据并将其导出到 [Power BI](https://powerbi.microsoft.com)。 

1. 在筛选框上方，单击依赖项列表顶部的“分析”  。 

    ![“分析”按钮](./media/luis-tutorial-appinsights/analytics-button.png)

2. 此时将打开一个新窗口，窗口顶部为一个查询窗口，查询窗口下方为一个数据表窗口。 如果之前使用过数据库，则会熟悉这种布局。 该查询包含过去 24 小时内以名称 `LUIS-results` 开头的所有项。 **CustomDimensions** 列以名称/值对的形式显示 LUIS 查询结果。

    ![运行 Analytics 查询窗口](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. 若要拉取首要意向、评分和陈述，请在查询窗口的最后一行上方添加以下内容：

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. 运行该查询。 滚动到数据表的最右侧。 此处显示新列“首要意向”、“评分”和“陈述”。 单击“首要意向”列进行排序。

    ![Analytics 首要意向](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


详细了解 [Kusto 查询语言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)或[将数据导出到 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 

## <a name="next-steps"></a>后续步骤

你可能还希望向 Application Insights 数据中添加其他信息，包括应用 ID、版本 ID、上次模型更改日期、上次训练日期和上次发布日期。 可从终结点 URL（应用 ID 和版本 ID ）或[创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 调用中检索这些值，然后在 Web 应用机器人设置中对值进行设置并从该位置拉取值。  

如果对多个 LUIS 应用使用同一个终结点订阅，则还应包含订阅 ID 和一个声明此为共享密钥的属性。 

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)




