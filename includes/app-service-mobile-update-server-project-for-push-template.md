在本部分中，更新现有移动应用后端项目中的代码，以便在每次添加新项目时推送通知。 此过程由 Azure 通知中心的[模板](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)功能提供支持，允许跨平台推送。 使用模板为推送通知注册了各种客户端，单个通用推送可到达所有客户端平台。

选择其中一个与后端项目类型 &mdash; [.NET 后端](#dotnet)或 [Node.js 后端](#nodejs)匹配的过程。

### <a name="dotnet"></a>.NET 后端项目
1. 在 Visual Studio 中，右键单击服务器项目。 然后选择“管理 NuGet 包”。 搜索 `Microsoft.Azure.NotificationHubs`，并选择“安装”。 此过程将安装通知中心库，以便从后端发送通知。
2. 在服务器项目中，打开“控制器” > “TodoItemController.cs”。 然后，添加以下 using 语句：

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

2. 在 PostTodoItem 方法中，在调用 InsertAsync 后添加如下代码：  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings = 
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Send the message so that all template registrations that contain "messageParam"
        // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    插入新项时，此过程将发送一个包含 item.Text 的模板通知。
4. 重新发布服务器项目。

### <a name="nodejs"></a>Node.js 后端项目
1. 如果尚未执行此操作，请[下载快速入门后端项目](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)，或使用 [Azure 门户中的在线编辑器](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。
2. 将 todoitem.js 文件中的现有代码替换为以下代码：

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK, 
    // see http://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    插入新项时，此过程将发送一个包含 item.text 的模板通知。
3. 编辑本地计算机上的文件时，请重新发布服务器项目。
