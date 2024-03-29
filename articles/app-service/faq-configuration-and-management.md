---
title: 配置常见问题解答 - Azure 应用服务 | Azure Docs
description: 获取有关 Azure App Service Web 应用功能配置和管理常见问题的解答。
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
origin.date: 05/11/2018
ms.date: 03/18/2019
ms.author: v-biyu
ms.openlocfilehash: 57bb731a35aa2a53ecb430f51a9126d898c7a89b
ms.sourcegitcommit: 0ccbf718e90bc4e374df83b1460585d3b17239ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57347179"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure Web 应用配置及管理常见问题解答

本文对 [Azure App Service Web 应用功能](https://www.azure.cn/home/features/app-service/web-apps/)配置和管理常见问题 (FAQ) 进行了解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>如果需要移动应用服务资源，是否有什么限制需要注意？

如果打算将应用服资源转移到新的资源组或订阅，有一些限制需要注意。 有关详细信息，请参阅[应用服务限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations)。

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>如何将自定义域名用于 Web 应用？

如需获取有关将自定义域名用于 Azure Web 应用的常见问题的解答，请观看七分钟视频[添加自定义域名](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)。 该视频演示如何添加自定义域名。 它介绍如何将自己的 URL 而非 *.chinacloudsites.cn URL 用于应用服务 Web 应用。 还可以看到有关[如何映射自定义域名](app-service-web-tutorial-custom-domain.md)的详细演示。

## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>如何上传和配置 Web 应用的现有 SSL 证书？

若要了解如何上传和设置现有的自定义 SSL 证书，请参阅[将现有的自定义 SSL 证书绑定到 Azure Web 应用](app-service-web-tutorial-custom-ssl.md#upload)。

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>可从何处找到指南清单，了解有关资源移动操作的详细信息？

[应用服务限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations)说明如何将资源移到新订阅或同一订阅的新资源组中。 可以获取有关资源移动清单的信息，了解哪些服务支持移动操作，并了解有关应用服务限制及其他主题的详细信息。

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>如何为 Web 应用设置服务器时区？

为 Web 应用设置服务器时区：

1. 在 Azure 门户的应用服务订阅中，转到“应用程序设置”菜单。
2. 在“应用设置”下，添加此设置：
    * 键 = WEBSITE_TIME_ZONE
    * 值 = *所需时区*
3. 选择“其他安全性验证” 。

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>为什么连续 Web 作业有时会失败？

默认情况下，如果 Web 应用已处于空闲状态相当一段时间，则其处于未加载的状态。 这样可以让系统节省资源。 在基本和标准计划中，可打开“Always On”设置，保持 Web 应用一直处于已加载的状态。 如果 Web 应用运行连续的 Web 作业，应启用“Always On”；否则，这些 Web 作业可能无法可靠运行。 有关详细信息，请参阅[创建连续运行的 Web 作业](webjobs-create.md#CreateContinuous)。

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>如何获取 Web 应用的出站 IP 地址？

获取 Web 应用出站 IP 地址列表：

1. 在 Azure 门户中的 Web 应用边栏选项卡上，转到“属性”菜单。
2. 搜索**出站 IP 地址**。

随即显示出站 IP 地址列表。

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>如何获取 Web 应用的保留或专用入站 IP 地址？

若要为针对 Azure 应用网站的入站调用设置专用的或保留的 IP 地址，请安装和配置基于 IP 的 SSL 证书。

请注意，若要将专用或保留 IP 地址用于入站调用，应用服务计划必须处于基本或更高服务计划中。

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>是否可以导出应用服务证书以在 Azure 外部使用（如用于在其他位置承载的网站）？ 

应用服务证书被视为 Azure 资源。 不应在 Azure 服务外部使用它们。 无法导出它们以在 Azure 外部使用。 有关详细信息，请参阅[应用服务证书和自定义域的常见问题解答](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>是否可以导出应用服务证书以用于其他 Azure 云服务？

门户针对通过 Azure Key Vault 将应用服务证书部署到应用服务应用提供一流的体验。 但是，我们从客户处收到了在应用服务平台外部使用这些证书（例如，用于 Azure 虚拟机）的请求。 若要了解如何创建应用服务证书的本地 PFX 副本以便可以将证书用于其他 Azure 资源，请参阅[创建应用服务证书的本地 PFX 副本](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)。

有关详细信息，请参阅[应用服务证书和自定义域的常见问题解答](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>当我尝试备份 Web 应用时，为何看到消息“已部分成功”？

备份失败的一个常见原因是应用程序正在使用某些文件。 执行备份时，正在使用的文件会被锁定。 这会阻止对这些文件的备份操作，并可能导致“部分成功”状态。 可以通过将文件从备份过程中排除来防止这种情况发生。 可以选择仅备份所需文件。 有关详细信息，请参阅 [Azure Web 应用仅备份站点的重要部分](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)。

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>如何从 HTTP 响应中删除标头？

若要移删除 HTTP 响应的标头，请更新站点的 web.config 文件。 有关详细信息，请参阅[在 Azure 网站上删除标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>应用服务是否符合 PCI 标准 3.0 和 3.1？

目前，Azure App Service 的 Web 应用功能符合 PCI 数据安全标准 (DSS) 3.0 版级别 1。 PCI DSS 3.1 版正在设计之中。 我们正在计划如何采用最新标准。

PCI DSS 3.1 版证书要求禁用传输层安全性 (TLS) 1.0。 目前，大多数应用服务计划无法禁用 TLS 1.0。

有关详细信息，请参阅 [App Service Web 应用 PCI 标准 3.0 和 3.1 符合性](https://support.microsoft.com/help/3124528)。

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>如何使用过渡环境和部署槽位？

在标准版和高级版应用服务计划中，将 Web 应用部署到应用服务时，可部署到单独的部署槽位而不是默认的生产槽位。 部署槽是具有自己的主机名的动态 Web 应用。 两个部署槽（包括生产槽）之间的 Web 应用内容与配置元素可以交换。

有关使用部署槽位的详细信息，请参阅[在应用服务中设置过渡环境](deploy-staging-slots.md)。

## <a name="how-do-i-access-and-review-webjob-logs"></a>如何访问和查看 Web 作业日志？

查看 Web 作业日志：

1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.chinacloudsites.cn)。
2. 选择 Web 作业。
3. 选择“切换输出”按钮。
4. 若要下载输出文件，请选择“下载”链接。
5. 对于单个运行，选择“单个调用”。
6. 选择“切换输出”按钮。
7. 选择下载链接。

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>我在尝试对 SQL Server 使用混合连接。 为什么会看到消息“System.OverflowException: 算术运算导致了溢出”？

如果使用混合连接访问 SQL Server，则 2016 年 5 月 10 的 Microsoft.NET 更新可能会导致连接失败。 你可能会看到此消息：

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>解决方法

该异常是由于混合连接管理器存在问题而导致，该问题现已修复。 请务必[更新混合连接管理器](https://go.microsoft.com/fwlink/?LinkID=841308)以解决此问题。

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>如何添加或编辑 URL 重写规则？

添加或编辑 URL 重写规则：

1. 设置 Internet Information Services (IIS) 管理器，以便将其连接到应用服务 Web 应用。 若要了解如何将 IIS 管理器连接到应用服务，请参阅[使用 IIS 管理器远程管理 Azure 网站](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/)。
2. 在 IIS 管理器中，添加或编辑 URL 重写规则。 若要了解如何添加或编辑一个 URL 重写规则，请参阅[为 URL 重写模块创建重写规则](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)。

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>如何控制应用服务的入站流量？

在站点层面上，有两种方法可用于控制应用服务的入站流量：

* 启用动态 IP 限制。 若要了解如何启用动态 IP 限制，请参阅[适用于 Azure 网站的 IP 和域限制](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)。
* 开启“模块安全”。 若要了解如何开启模块安全，请参阅 [Azure 网站上的 ModSecurity Web 应用程序防火墙](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)。

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>如何在应用服务 Web 应用中封锁端口？

在应用服务共享租户环境中，由于基础结构的性质，无法封锁特定端口。 TCP 端口 4016、 4018 和 4020 也可能处于打开状态，用于进行 Visual Studio 远程调试。

## <a name="how-do-i-capture-an-f12-trace"></a>如何捕获 F12 跟踪？

捕获 F12 跟踪有两种方法：

* F12 HTTP 跟踪
* F12 控制台输出

### <a name="f12-http-trace"></a>F12 HTTP 跟踪

1. 在 Internet Explorer 中，转到网站。 请务必先登录，然后再执行后续步骤。 否则，F12 跟踪会捕获敏感登录数据。
2. 按 F12。
3. 确认已选中“网络”选项卡，然后选中绿色“播放”按钮。
4. 执行可重现问题的步骤。
5. 选择红色“停止”按钮。
6. 选择“保存”按钮（磁盘图标），保存 HAR 文件（在 Internet Explorer 和 Microsoft Edge 中）*或者*右键单击 HAR 文件，然后选择“内容另存为 HAR”（在 Chrome 中）。

### <a name="f12-console-output"></a>F12 控制台输出

1. 选择“控制台”选项卡。
2. 对于每个至少包含一项的选项卡，选择选项卡（“错误”、“警告”或“信息”）。 如果未选中选项卡，移开光标时，选项卡图标呈灰色或黑色。
3. 右键单击窗格中的信息区域，然后选择“全部复制”。
4. 将复制的文本粘贴到文件中，然后保存该文件。

若要查看 HAR 文件，可以使用 [HAR 查看器](https://www.softwareishard.com/har/viewer/)。

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>在我尝试将应用服务 Web 应用到连接到与 ExpressRoute 连接的虚拟网络时，为何会遇到错误？

如果尝试将 Azure Web 应用连接到与 Azure ExpressRoute 连接的虚拟网络，则会失败。 此时会显示以下消息：“网关不是 VPN 网关”。

当前，无法与连接到 ExpressRoute 的虚拟网络建立点到站点 VPN 连接。 对于同一虚拟网络而言，点到站点 VPN 和 ExpressRoute 不能共存。 有关详细信息，请参阅 [ExpressRoute 和站点到站点 VPN 连接限制和局限性](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)。

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>如何将应用服务 Web 应用连接到具有静态路由（基于策略）网关的虚拟网络？

当前，不支持将应用服务 Web 应用连接到具有静态路由（基于策略）网关的虚拟网络。 如果目标虚拟网络已经存在，必须在连接到应用之前借助动态路由网关使网络处于点到站点 VPN 启用状态。 如果网关设置为静态路由，则无法启用点到站点 VPN。 

有关详细信息，请参阅[将应用与 Azure 虚拟网络进行集成](web-sites-integrate-with-vnet.md#getting-started)。
## <a name="why-cant-i-delete-my-app-service-plan"></a>为什么无法删除应用服务计划？

如果应用服务计划与任何应用服务应用相关联，则无法删除此应用服务计划。 删除应用服务计划前，请从应用服务计划中删除所有关联的应用服务应用。

## <a name="how-do-i-schedule-a-webjob"></a>如何计划 Web 作业？

可以使用 Cron 表达式创建计划的 Web 作业：

1. 创建一个 settings.job 文件。
2. 在此 JSON 文件中，使用 Cron 表达式将计划属性包括在内： 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

有关计划 Web 作业的详细信息，请参阅[使用 Cron 表达式创建计划 Web 作业](webjobs-create.md#CreateScheduledCRON)。

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>如何对应用服务应用执行渗透测试？

若要执行渗透测试，请[提交请求](https://portal.msrc.microsoft.com/en-us/engage/pentest)。

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>如何为使用流量管理器的应用服务 Web 应用配置自定义域名？

若要了解如何对使用 Azure 流量管理器进行负载平衡的应用服务应用使用自定义域名，请参阅[为使用流量管理器的 Azure Web 应用配置自定义域名](web-sites-traffic-manager-custom-domain-name.md)。

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>我的应用服务证书被标记为存在欺诈。 如何解决此问题？

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在应用服务证书购买的域验证过程中，可能会看到以下消息：

“你的证书已被标记为可能存在欺诈。 请求当前正在审查中。 如果证书未在 24 小时内变为可用，请联系 Azure 支持部门。”

如该消息所示，此欺诈验证过程可能需要最多 24 小时才能完成。 在此期间，你会继续看到该消息。

如果你的应用服务证书在 24 小时后继续显示此消息，请运行以下 PowerShell 脚本。 该脚本会联系[证书提供商](https://www.godaddy.com/)直接解决问题。

```
Connect-AzAccount -Environment AzureChinaCloud
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>应用服务中是如何进行身份验证和授权的？

有关应用服务中的身份验证和授权的详细文档，请参阅各种标识提供者登录的文档：
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Microsoft 帐户](configure-authentication-provider-microsoft.md)

## <a name="how-do-i-redirect-the-default-chinacloudsitescn-domain-to-my-azure-web-apps-custom-domain"></a>如何将默认 *.chinacloudsites.cn 域重定向到 Azure Web 应用的自定义域？

在 Azure 中使用 Web 应用创建新网站时，将向你的站点分配一个默认 *sitename*.chinacloudsites.cn 域。 如果将自定义主机名添加到站点，并且不希望用户能够访问默认 *.chinacloudsites.cn 域，可以将默认 URL 进行重定向。 若要了解如何将源自网站默认域的所有通信流重定向到自定义域，请参阅[将默认域重定向到 Azure Web 应用中的自定义域](http://zainrizvi.io/blog/block-default-azure-websites-domain/)。

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>如何确定应用服务中安装的 .NET 版本？

若要查找应用服务中安装的 Microsoft.NET 版本，最快的方法是使用 Kudu 控制台。 可以从门户或使用应用服务应用的 URL，来访问 Kudu 控制台。 有关详细说明，请参阅[确定应用服务中安装的 .NET 版本](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/)。

## <a name="why-isnt-autoscale-working-as-expected"></a>为什么自动缩放不按预期方式工作？

如果 Azure 自动缩放未按预期方式缩放 web 应用，可能会陷入一种困境，在这种情况下，建议主动选择不进行缩放以避免由“不稳定”引起的无限循环。 当扩大与缩小阈值之间没有足够空间时，通常会发生这种情况。 若要了解如何避免“波动”以及如何了解其他自动缩放最佳做法，请参阅[自动缩放最佳做法](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)。

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>为何自动缩放有时只部分缩放？

当指标超过预配置的限值时，将触发自动缩放。 有时可能会发现，与预期相比，仅填充了部分容量。 当所需的实例数无法实现时，则可能会发生这种情况。 在这种情况下，自动缩放使用可用的实例数进行部分填充。 然后，自动缩放运行重新平衡逻辑，以获取更多容量。 它会分配剩余实例。 请注意，这可能需要几分钟的时间。

如果在几分钟后未看到预期数量的实例，则可能是因为部分重填已足以使指标处于边界内。 或者，可能自动缩放已执行缩小操作，因为已达到度量值的下限。

如果以上情况都不存在而问题仍然存在，请提交支持请求。

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>如何为内容启用 HTTP 压缩？

若要同时为静态和动态内容类型启用压缩，请将以下代码添加到应用程序级别的 web.config 文件：

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

还可以指定要压缩的特定动态和静态 MIME 类型。 有关详细信息，请参阅我们在[简单 Azure 网站上的 httpCompression 设置](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)中对论坛问题的回复。

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>如何从本地环境迁移到应用服务？

若要将站点从 Windows Web 服务器迁移到应用服务，可以使用 Azure 应用服务迁移助手。 该迁移工具会根据需要在 Azure 中创建 Web 应用和数据库，然后发布内容。 有关详细信息，请参阅 [Azure App Service 迁移助手](https://www.migratetoazure.net/)。
