---
title: 配置应用 - Azure 应用服务
description: 如何在 Azure 应用服务中配置应用
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/25/2017
ms.date: 03/25/2019
ms.author: v-biyu
ms.custom: seodec18
ms.openlocfilehash: cb988f8555ea1eb75fdcd4a40c0ce6c0292bd91e
ms.sourcegitcommit: b1a411528581081a0c93f44741a29bdd6b450f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57787317"
---
# <a name="configure-apps-in-azure-app-service"></a>在 Azure 应用服务中配置应用

本主题介绍如何使用 [Azure 门户]配置 Web 应用、移动后端或 API 应用。

## <a name="application-settings"></a>应用程序设置
1. 在 [Azure 门户]中，打开应用的边栏选项卡。
2. 单击“新建”&gt;“Web + 移动”&gt;“Web 应用” **应用程序设置**。

![应用程序设置][configure01]

“应用程序设置”  边栏选项卡包含多个类别的设置。

### <a name="general-settings"></a>常规设置
**框架版本**。 如果应用使用下列任一框架，请设置这些选项： 

* **.NET Framework**：设置 .NET Framework 版本。 
* **PHP**：设置 PHP 版本或设为“关”以禁用 PHP。 
* **Java**：选择 Java 版本或设为“关”以禁用 Java。 利用“Web 容器”选项来选择 Tomcat 或 Jetty 版本。
* **Python**：选择 Python 版本，或设为“关”以禁用 Python。

出于技术原因，为应用启用 Java 会禁用 .NET、PHP 和 Python 选项。

<a name="platform"></a>
**平台**。 选择要在 32 位还是 64 位环境中运行应用。 64 位环境需要“基本”或“标准”层。 “免费”和“共享”层始终在 32 位环境下运行。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web 套接字**。 设为“开”以启用 WebSocket 协议；例如，如果应用使用 [ASP.NET SignalR] 或 [socket.io](https://socket.io/)。

<a name="alwayson"></a>
**始终打开**。 默认情况下，应用如果已处于空闲状态达到一定时间，则会卸载。 这样可以让系统节省资源。 在“基本”或“标准”模式下，可启用“AlwaysOn”  以保证始终加载应用。 如果应用运行连续的 Web 作业或运行使用 CRON 表达式触发的 Web 作业，应启用“始终可用”；否则这些 Web 作业可能无法可靠运行。

**托管管道版本**。 设置 IIS [管道模式]。 将此设置保留为“集成(默认)”，除非旧版应用需要旧版 IIS。

**HTTP 版本**。 设置为 **2.0**，以启用对 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 协议的支持。 

**ARR 相关性**。 在横向扩展到多个 VM 实例的应用中，ARR 相关性 Cookie 保证在会话的整个生命周期内将客户端路由到相同的实例。 若要提高无状态应用程序的性能，请将此选项设置为“关闭”。   

**自动交换**。 如果启用部署槽的自动交换，则在向该槽推送更新时，应用服务会自动将应用交换到生产。 有关详细信息，请参阅[为 Azure 应用服务中的应用部署到过渡槽](deploy-staging-slots.md)。

### <a name="debugging"></a>调试
**远程调试**。 启用远程调试。 启用后，可使用 Visual Studio 中的远程调试器直接连接到应用。 远程调试将保持启用状态 48 小时。 

### <a name="app-settings"></a>应用设置
本部分包含应用启动时会加载的名称/值对。 

* 对于 .NET 应用，这些设置会在运行时注入到 .NET 配置 `AppSettings` 中，重写现有设置。 
* PHP、Python、Java 和 Node 应用程序可以在运行时以环境变量的形式访问这些设置。 系统将为每个应用程序设置创建两个环境变量，一个变量具有由应用程序设置条目指定的名称，另一个具有 APPSETTING_ 前缀。 这两个变量都包含相同的值。

应用程序设置在存储时始终进行加密（静态加密）。

可以使用[密钥保管库引用](app-service-key-vault-references.md)从密钥保管库解析应用设置。

### <a name="connection-strings"></a>连接字符串
链接资源的连接字符串。 

对于 .NET 应用，这些连接字符串会在运行时注入到 .NET 配置 `connectionStrings` 设置中，重写其中的键等于链接的数据库名称的所有现有条目。 

对于 PHP、Python、Java 和 Node 应用程序，这些设置会在运行时作为环境变量提供，并且用连接类型作为前缀。 下面列出了环境变量前缀： 

* SQL Server： `SQLCONNSTR_`
* MySQL： `MYSQLCONNSTR_`
* SQL 数据库： `SQLAZURECONNSTR_`
* 自定义：`CUSTOMCONNSTR_`

例如，如果 MySql 连接字符串命名为 `connectionstring1`，则会通过环境变量 `MYSQLCONNSTR_connectionString1` 访问该字符串。

连接字符串在存储时始终进行加密（静态加密）。

可以使用[密钥保管库引用](app-service-key-vault-references.md)从密钥保管库解析连接字符串。

### <a name="default-documents"></a>默认文档
默认文档是在网站的根 URL 下显示的网页。  使用列表中第一个匹配文件。 

应用可能会使用根据 URL 路由的模块，而不是提供静态内容，在此情况下，将没有此类默认文档。    

### <a name="handler-mappings"></a>处理程序映射
使用此区域可添加自定义脚本处理器，以处理特定文件扩展名的请求。 

* **扩展名**。 要处理的扩展名，例如 *.php 或 handler.fcgi。 
* **脚本处理器路径**。 脚本处理器的绝对路径。 与文件扩展名匹配的文件请求由脚本处理器处理。 使用路径 `D:\home\site\wwwroot` 表示应用的根目录。
* **其他参数**。 脚本处理器的可选命令行参数 

### <a name="virtual-applications-and-directories"></a>虚拟应用程序和目录
若要配置虚拟应用程序和目录，请指定每个虚拟目录及其对应于网站根目录的物理路径。 还可选中“应用程序”复选框，将虚拟目录标记为应用程序。

## <a name="enabling-diagnostic-logs"></a>启用诊断日志
启用诊断日志：

1. 在应用的边栏选项卡上单击“所有设置”。
2. 单击“诊断日志” . 

从支持日志记录的 Web 应用程序写入诊断日志的选项： 

* **应用程序日志记录**。 将应用程序日志写入文件系统。 日志记录将持续 12 小时。 

**级别**。 启用应用程序日志记录时，此选项指定要记录的信息量（“错误”、“警告”、“信息”或“详细”）。

**Web 服务器日志记录**。 日志以 W3C 扩展日志文件格式保存。 

**详细的错误消息**。 保存详细的错误消息 .htm 文件。 文件保存在 /LogFiles/DetailedErrors 下。 

**失败请求跟踪**。 将失败请求记录到 XML 文件中。 这些文件保存在 /LogFiles/W3SVC*xxx*下，其中 xxx 是唯一标识符。 此文件夹包含一个 XSL 文件和一个或多个 XML 文件。 请务必下载 XSL 文件，因为 XSL 文件提供格式化和筛选 XML 文件内容的功能。

若要查看日志文件，必须按以下方式创建 FTP 凭据：

1. 在应用的边栏选项卡上单击“所有设置”。
2. 单击“部署凭据” 。
3. 输入用户名和密码。
4. 单击“保存”。

![设置部署凭据][configure03]

完整的 FTP 用户名是“app\username”，其中 app 是应用的名称。 用户名列在应用边栏选项卡的“软件包”下。

![FTP 部署凭据][configure02]

## <a name="other-configuration-tasks"></a>其他配置任务
### <a name="ssl"></a>SSL
在“基本”或“标准”模式下，可为自定义域上传 SSL 证书。 有关详细信息，请参阅[为应用启用 HTTPS](app-service-web-tutorial-custom-ssl.md)。 

若要查看上传的证书，请单击“所有设置” > “自定义域和 SSL”。

### <a name="domain-names"></a>域名
添加应用的自定义域名。 有关详细信息，请参阅[为 Azure 应用服务中的应用配置自定义域名](app-service-web-tutorial-custom-domain.md)。

若要查看域名，请单击“所有设置” > “自定义域和 SSL”。

### <a name="deployments"></a>部署
* 设置连续部署。 请参阅[使用 Git 在 Azure 应用服务中部署应用](deploy-local-git.md)。
* 部署槽。 请参阅[为 Azure 应用服务部署到过渡环境]。

若要查看部署槽，请单击“所有设置” > “部署槽”。

### <a name="monitoring"></a>监视
在“基本”或“标准”模式下，可以测试 HTTP 或 HTTPS 终结点的可用性，最多可测试三个地理分散的位置。 如果 HTTP 响应码为错误（4xx 或 5xx），或者响应时间超过 30 秒，则表示监视测试失败。 如果从所有指定的位置监视测试均成功，则终结点被视为可用。 

有关更多信息，请参阅[如何：监视 Web 终结点状态]。

## <a name="next-steps"></a>后续步骤
* [在 Azure 应用服务中配置自定义域名]
* [为 Azure 应用服务中的应用启用 HTTPS]
* [在 Azure 应用服务中缩放应用]
* [在 Azure 应用服务中监视基础知识]

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 门户]: https://portal.azure.cn/
[在 Azure 应用服务中配置自定义域名]: ./app-service-web-tutorial-custom-domain.md
[为 Azure 应用服务部署到过渡环境]: ./deploy-staging-slots.md
[为 Azure 应用服务中的应用启用 HTTPS]: ./app-service-web-tutorial-custom-ssl.md
[如何：监视 Web 终结点状态]: ./web-sites-monitor.md
[在 Azure 应用服务中监视基础知识]: ./web-sites-monitor.md
[管道模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure 应用服务中缩放应用]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
