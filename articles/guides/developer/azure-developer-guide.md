---
title: 面向 Azure 开发人员的入门指南 | Microsoft 文档
description: 本主题提供的基本信息适用于那些希望使用 Azure 平台进行开发的开发人员。
services: ''
cloud: ''
documentationcenter: ''
author: forester123
manager: digimobile
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/18/2017
ms.date: 04/17/2019
ms.author: v-johch
ms.openlocfilehash: e52959ab4e03a7faf97d4fbf81df3fef228bc51a
ms.sourcegitcommit: bf3df5d77e5fa66825fe22ca8937930bf45fd201
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59686479"
---
# <a name="get-started-guide-for-azure-in-china-developers"></a>中国区 Azure 开发人员入门指南

## <a name="what-is-azure"></a>什么是 Azure？

Azure 是一个完整的云平台，可以托管你现有的应用程序，简化新应用程序的开发，甚至还可以增强本地应用程序的功能。 在充分利用云计算效率的同时，Azure 集成了开发、测试、部署和管理应用程序所需的各种云服务。

通过在 Azure 中托管应用程序，你可以随着客户需求的增长，从小规模开始轻松扩展应用程序。 另外，Azure 还可以针对高可用性应用程序提供所需的可靠性，甚至包括在两个不同区域之间的故障转移。 通过 [Azure 门户](https://portal.azure.cn)，可让你轻松管理所有的 Azure 服务。 同时，你还可以通过使用特定于服务的 API 和模板以编程方式管理你的服务。

**目标读者**：本指南介绍面向应用程序开发人员的 Azure 平台。 针对在 Azure 中开始生成新的应用程序或将现有应用程序迁移到 Azure 所需的操作，提供相关指导和说明。

## <a name="where-do-i-start"></a>从哪里开始？

面对 Azure 提供的众多服务，想要从中找出支持你解决方案体系结构的服务，也是一项艰巨的任务。 本部分将重点介绍开发人员通常使用的 Azure 服务。 有关所有 Azure 服务列表，请参阅 [Azure 文档](../../index.md)。

首先，你必须确定在 Azure 中托管应用程序的方式。 是否需要将整个基础结构作为一台虚拟机 (VM) 来管理。 是否可以使用 Azure 提供的平台管理功能？ 或许你只需要一个无服务器框架来托管代码执行？

应用程序需要云存储，Azure 为此提供了若干选项。 你可以利用 Azure 的企业身份验证。 此外，还有用于基于云开发和监视的各种工具，并且大多数托管服务都提供 DevOps 集成。

现在，让我们先看一下我们建议的用于应用程序调查的特定服务。

### <a name="application-hosting"></a>应用程序托管

Azure 提供了多个基于云的计算服务来运行你的应用程序，因此，你无需担心基础结构详细信息。 随着应用程序使用率的增长，可轻松纵向扩展或横向扩展你的资源。

Azure 提供了支持应用程序开发和托管需求的服务。 Azure 还提供了基础结构即服务 (IaaS)，以便让你完全控制应用程序托管。 Azure 的平台即服务 (PaaS) 产品提供了支持应用所需的完全托管服务。 在 Azure 中甚至还有真正的无服务器托管，你在其中需要做的就是编写代码。

![Azure 应用程序托管选项](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure 应用服务 

如果你希望以最快路径发布基于 Web 的项目，则可以考虑使用 Azure 应用服务。 通过应用服务，可以轻松扩展 Web 应用以支持移动客户端，并发布易于使用的 REST API。 此平台通过使用社交提供程序、基于流量的自动缩放、在生产中测试和基于容器的持续部署来提供身份验证。

可以创建 Web 应用、移动应用后端和 API 应用。

由于所有上述三个应用类型均共享应用服务运行时，因此，你可以托管网站，支持移动客户端，并在 Azure 中公开你的 API，所有这些均可在同一个项目或解决方案中完成。 若要详细了解应用服务，请参阅[什么是 Azure Web 应用](../../app-service/overview.md)。

应用服务在设计之初就考虑到了 DevOps。 它可以支持各种用于发布和持续集成部署的工具，包括 GitHub webhook、Jenkins、Azure DevOps、TeamCity 等。

> **入门**：通过应用服务，可以轻松创建和部署你的第一个 [Web 应用](../../app-service/app-service-web-get-started-dotnet.md)、[移动应用](../../app-service-mobile/app-service-mobile-ios-get-started.md)或 [API 应用](../../app-service/app-service-web-tutorial-rest-api.md)。

#### <a name="azure-virtual-machines"></a>Azure 虚拟机

作为一个基础结构即服务 (IaaS) 提供程序，Azure 可让你部署到或将应用程序迁移到 Windows 或 Linux VM。 Azure 虚拟机连同 Azure 虚拟网络一起，可支持将 Windows 或 Linux VM 部署到 Azure。 通过 VM，你可以全面控制计算机的配置。 使用 VM 时，你将负责所有服务器软件的安装、配置、维护和操作系统修补。

由于你对 VM 拥有所有控制级别，因此，你可以在 Azure 上运行大量不适合 PaaS 模型的服务器工作负载。 这些工作负载可包括数据库服务器、Windows Server Active Directory 和 Microsoft SharePoint。 有关详细信息，请参阅 [Linux](/virtual-machines/linux/) 或 [Windows](/virtual-machines/windows/) 的虚拟机文档。

> **何时使用**：在需要完全控制应用程序基础结构的情况下，或者需要将本地应用程序工作负载迁移到 Azure 而无需进行更改的情况下，可以使用虚拟机。
> 
> **入门**：从 Azure 门户创建 [Linux VM](../../virtual-machines/linux/quick-create-portal.md) 或 [Windows VM](../../virtual-machines/windows/quick-create-portal.md)。

#### <a name="azure-functions-serverless"></a>Azure Functions（无服务器）

你无需担心构建和管理整个应用程序或运行代码的基础结构。 而是，只需编写代码并以响应事件的方式或按计划来运行代码，那会是怎样？  [Azure Functions](../../azure-functions/functions-overview.md) 是一个“无服务器”样式的产品，可让你仅编写所需的代码。 借助 Functions，将通过 HTTP 请求、webhook、云服务事件，或按计划触发代码执行。 你可以使用所选的开发语言开发编码，如 C\#、F\#、Node.js、Python 或 PHP。 使用基于消耗的计费方式，只需要支付代码执行的时间，并且 Azure 可根据需要进行扩展。

> **何时使用**：在代码是由其他 Azure 服务或基于 Web 的事件触发或者按计划触发的情况下，可以使用 Azure Functions。 此外，当你不需要完整托管项目的开销或者只想支付代码运行时间时，也可以使用 Functions。 有关详细信息，请参阅 [Azure Functions 概览](../../azure-functions/functions-overview.md)。
> 
> **入门**：按照 Functions 快速入门教程，从门户[创建你的第一个函数](../../azure-functions/functions-create-first-azure-function.md)。
> 

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric 是一个分布式系统平台，适用于生成、打包、部署和管理可缩放的可靠微服务。 它还提供了全面的应用程序管理功能，用于设置、部署、监视、升级/修补和删除部署的应用程序。 在共享计算机池上运行的应用可以从小规模开始，再根据需要扩展为成百上千个计算机。

Service Fabric 支持具有 Open Web Interface for .NET (OWIN) 和 ASP.NET Core 的 WebAPI。 它提供了用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 若要详细了解 Service Fabric，请参阅 [Service Fabric 文档](/service-fabric/)。

> **何时使用：** 如果要创建应用程序或重新编写现有应用程序，以使用微服务体系结构，则 Service Fabric 将是一个不错的选择。 当你需要更好地控制或直接访问底层基础结构时，也可以使用 Service Fabric。
> 
> **入门：**[创建第一个 Azure Service Fabric 应用程序](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md)。

### <a name="enhance-your-applications-with-azure-services"></a>使用 Azure 服务增强应用程序

除了应用程序托管，Azure 还在云中和本地提供了可以增强应用程序功能、开发和维护的服务产品。

#### <a name="hosted-storage-and-data-access"></a>托管存储和数据访问

大多数应用程序都必须存储数据，因此，无论你决定如何在 Azure 中托管应用程序，都要考虑下面一个或多个存储和数据服务。

- **Azure Cosmos DB**：一个全球分布式多模型数据库服务，使用它可以跨任意数量的地理区域，根据全面的 SLA，灵活地缩放吞吐量与存储。 
  > **何时使用：** 当应用程序需要文档、表或图形数据库（包括具有多个妥善定义的一致性模型的 MongoDB 数据库）时。 
  > 
  > **入门**：[构建 Azure Cosmos DB Web 应用](../../cosmos-db/create-sql-api-dotnet.md)。 如果是 MongoDB 开发人员，请参阅[构建使用 Azure Cosmos DB 的 MongoDB Web 应用](../../cosmos-db/create-mongodb-dotnet.md)。

- **Azure 存储**：可针对 Blob、队列、文件和其他类型的非关系数据提供持久且可用性高的存储。 存储服务为 VM 提供了存储基础。

  > **何时使用**：在应用存储非关系数据，如键值对（表）、Blob、文件共享，或消息（队列）的情况下使用。
  > 
  > **入门**：选择这些类型的存储之一：[Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)、[表](../../cosmos-db/table-storage-how-to-use-dotnet.md)、[队列](../../storage/queues/storage-dotnet-how-to-use-queues.md)或[文件](../../storage/files/storage-dotnet-how-to-use-files.md)。

- **Azure SQL 数据库**：基于 Azure 的 Microsoft SQL Server 引擎版本，用于在云中存储关系表格数据。 SQL 数据库可提供可预测的性能、在不停机情况下进行缩放、业务连续性和数据保护功能。

  > **何时使用**：在应用程序需要具有引用完整性、事务支持和 TSQL 查询支持的数据存储时，可以考虑使用。
  > 
  > **入门**：[使用 Azure 门户在几分钟内创建 SQL 数据库](../../sql-database/sql-database-get-started.md)。


可以使用 Azure 数据工厂将现有的本地数据移到 Azure。 如果尚未做好将数据移到云中的准备，可以使用 BizTalk 服务中的混合连接将应用服务托管的应用连接到本地资源。 此外，你还可以从本地应用程序连接到 Azure 数据和存储服务。

>[!NOTE]
>Azure 数据工厂和 BizTalk 服务混合连接在中国不可用。
>
>

#### <a name="docker-support"></a>Docker 支持

>[!NOTE]
>Docker 和容器服务在中国不可用。
>
>

Docker 容器是操作系统虚拟化的一种形式，可让你以更高效且可预见的方式部署应用程序。 容器化应用程序在生产环境中的工作方式与在开发和测试系统上的工作方式相同。 你可以通过使用标准的 Docker 工具来管理容器。 可以使用现有技能和常用的开源工具在 Azure 上部署和管理基于容器的应用程序。

Azure 提供了多种方式以便在应用程序中使用容器。

-   **Azure Docker VM 扩展**：允许使用 Docker 工具将 VM 配置为一个 Docker 主机。

    >**何时使用**：需要在 VM 上为应用程序生成一致的容器部署时，或者需要使用 [Docker Compose](https://docs.docker.com/compose/overview/) 时。

-   **Azure 容器服务**：允许你创建、配置和管理预配置的用于运行容器化应用程序的虚拟机群集。 

    >**何时使用**：在需要构建生产就绪型可缩放环境以提供其他计划和管理工具时使用，或者在部署 Docker Swarm 群集时使用。

-   **Docker 计算机**：允许使用 docker 计算机命令在虚拟主机上安装和管理 Docker 引擎。

    >**何时使用**：当需要通过创建一个 Docker 主机将应用快速原型化时使用。

-   **应用服务的自定义 Docker 映像**：在 Linux 上部署 Web 应用时，可以使用容器注册表或客户容器中的 Docker 容器。

    >**何时使用**：在 Linux 上将 Web 应用部署到 Docker 映像时使用。


### <a name="authentication"></a>身份验证

重要的是：不仅要知道谁在使用你的应用程序，而且还要防止对资源的未授权访问。 Azure 提供了多种方式来对你的应用客户端进行身份验证。

- **Azure Active Directory (Azure AD)**：Microsoft 基于云的多租户标识和访问管理服务。 你可以通过与 Azure AD 集成将单一登录 (SSO) 添加到你的应用程序。 可以通过直接使用 Azure AD Graph API 或 Microsoft Graph API 访问目录属性。 可以通过使用本机 HTTP/REST 终结点和多平台 Azure AD 身份验证库与支持 OAuth2.0 授权框架的 Azure AD 和 Open ID Connect 相集成。

  > **何时使用**：当需要提供 SSO 体验、使用基于 Graph 的数据，或者对基于域的用户进行身份验证时使用。
  > 
  > **入门**：若要了解详细信息，请参阅 [Azure Active Directory 开发人员指南](../../active-directory/develop/v1-overview.md)。

- **应用服务身份验证**：选择应用服务来托管应用时，还会获得对 Azure AD 的内置身份验证支持。

  > **何时使用**：在需要通过 Azure AD 和/或社交标识提供者在应用服务应用中启用身份验证时使用。
  > 
  > **入门**：若要了解有关应用服务中身份验证的详细信息，请参阅 [Azure 应用服务中的身份验证和授权](../../app-service/overview-authentication-authorization.md)。


### <a name="monitoring"></a>监视

当应用程序在 Azure 中运行时，需要能够监视性能、关注问题，并了解客户使用应用的方式。 为此，Azure 提供了几个监视选项。

- **Visual Studio Application Insights**：一项 Azure 托管的可扩展分析服务，与 Visual Studio 集成后可监视实时 Web 应用。 该服务可为你提供需要不断改进应用性能和可用性的数据，无论它们是否托管在 Azure 上。
  >[!NOTE]
  >Application Insights 在中国不可用。
  >
  >

- **Azure Monitor**：这项服务可帮助你对由 Azure 基础结构和资源生成的指标和日志进行可视化、查询、路由、存档，并对其执行其他操作。 该监视器将提供你在 Azure 门户中看到的数据视图，并且是用于监视 Azure 资源的单一源。
 
   >**入门**：[Azure Monitor 入门](../../monitoring-and-diagnostics/monitoring-get-started.md)。

### <a name="devops-integration"></a>DevOps 集成

无论是预配虚拟机还是发布持续集成的 Web 应用，Azure 都将与大部分常用的 DevOps 工具集成。 在对 Jenkins、GitHub、Puppet、Chef、TeamCity、Ansible、Azure DevOps 以及其他工具的支持下，你可以使用已有工具并在最大程度上提升现有体验。



> **入门**：如需查看应用服务应用的 DevOps 选项，请参阅[对 Azure 应用服务的持续部署](../../app-service/deploy-continuous-deployment.md)。


## <a name="azure-regions"></a>Azure 区域

Azure 是一个全球性云平台，在世界各地的许多区域都可以使用。 当你在 Azure 中设置服务、应用程序或虚拟机时，系统将要求你选择一个表示特定数据中心的区域，将在其中运行应用程序或存储数据。 这些区域对应于在 [Azure 区域](https://azure.microsoft.com/regions/)页上发布的特定位置。

>[!NOTE]
>Azure 中国在以下四个区域中提供：中国东部、中国东部 2、中国北部和中国北部 2。

### <a name="choose-the-best-region-for-your-application-and-data"></a>选择应用程序和数据的最佳区域

使用 Azure 的好处之一是，你可以将应用程序部署到全球范围内的各种数据中心。 你选择的区域可能会影响应用程序的性能。 例如，最好选择更接近于大部分客户的区域，以减少网络请求中的延迟。 你也可以选择你的地区，以符合在某些国家/地区分发应用的法律要求。 最佳的做法是，始终将应用程序数据存储在相同的数据中心，或尽可能靠近托管应用程序的数据中心的数据中心。

### <a name="multi-region-apps"></a>多区域应用

尽管整个数据中心可能会因自然灾害或 Internet 故障等事件而脱机，不过这种情况极少发生。 最佳的做法是，将至关重要的业务应用程序托管在多个数据中心，以提供最大的可用性。 另外，使用多区域也可以减少全球用户的延迟，并在更新应用程序时提供更多的弹性。

某些服务，如虚拟机和应用服务，使用 [Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)启用在两个区域之间进行故障转移的多区域支持，以支持高可用性的企业应用程序。 

>**何时使用**：当拥有的企业和高可用性应用程序受益于故障转移和复制时使用。

## <a name="how-do-i-manage-my-applications-and-projects"></a>如何管理我的应用程序和项目？

Azure 提供了一套丰富的体验，可让你同时以编程方式和在 [Azure 门户](https://portal.azure.cn/)中创建并管理你的 Azure 资源、应用程序和项目。

### <a name="command-line-interfaces-and-powershell"></a>命令行接口和 PowerShell

Azure 提供了两种方式（通过使用 Bash、终端、命令提示符或你所选的命令行工具）从命令行中管理应用程序和服务。 通常情况下，你可以从命令行执行与在 Azure 门户中一样任务，例如，创建和配置虚拟机、虚拟网络、Web 应用以及其他服务。

-   [Azure 命令行界面 (CLI)](../../xplat-cli-install.md)：允许你连接到 Azure 订阅，并从命令行根据 Azure 资源对各种任务进行编程。

-   [Azure PowerShell](../../powershell-install-configure.md)：提供了一组带 cmdlet 的模块，可让你通过 Windows PowerShell 来管理 Azure 资源。

### <a name="azure-portal"></a>Azure 门户

Azure 门户是一个基于 Web 的应用程序，可用于创建、管理和删除 Azure 资源及服务。 Azure 门户位于 <https://portal.azure.cn>。 它包括可自定义的仪表板、用于管理 Azure 资源的工具，以及对订阅设置和计费信息的访问权限。 有关详细信息，请参阅 [Azure 门户概述](../../azure-portal-overview.md)。

### <a name="rest-apis"></a>REST API

Azure 是基于一组支持 Azure 门户 UI 的 REST API 构建的。 其中大多数 REST API 还支持通过编程方式设置和管理来自任何启用 Internet 设备的 Azure 资源和应用程序。 有关完整的 REST API 文档集，请参阅 [Azure REST SDK 参考](https://docs.microsoft.com/rest/api/)。

### <a name="apis"></a>API

除了 REST API，许多 Azure 服务还允许以编程方式管理应用程序中的资源，方法是通过使用特定于平台的 Azure SDK，包括用于以下开发平台的 SDK：

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](/java/api/)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

[移动应用](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md)和 [Azure 媒体服务](../../media-services/previous/media-services-dotnet-how-to-use.md)等服务均提供客户端 SDK，可让你从 Web 和移动客户端应用访问服务。

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
在 Azure 上运行应用可能需要使用多个 Azure 服务，所有这些服务都遵循相同的生命周期，并且可以被视为一个逻辑单元。 例如，Web 应用可以使用 Web 应用、SQL 数据库、存储、Azure Redis 缓存，以及 Azure 内容分发网络服务。 [Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)能够使你以组的方式处理应用程序中的资源。 你可以通过单个协调的操作来部署、更新或删除所有资源。

除了逻辑分组和管理相关的资源外，Azure 资源管理器还包括可让你自定义相关资源部署和配置的部署功能。 例如，通过使用资源管理器，你可以将包含多个虚拟机、负载均衡器和 Azure SQL 数据库的应用程序作为单一单元进行部署和配置。

可使用 Azure 资源管理器模板（JSON 格式的文档）来开发这些部署。 借助这些模板，可使用声明性模板（而不是脚本）来定义应用程序的部署和管理。 模板可用于测试、暂存和生产等不同的环境。 例如，通过使用模板，可将一个按钮添加到 GitHub 存储库，然后只需单击一下即可将存储库中的代码部署到一组 Azure 服务中。

> **何时使用**：在需要对应用执行基于模板的部署时，可以使用资源管理器模板。该应用可以通过 REST API、Azure CLI 和 Azure PowerShell 以编程方式进行管理。
> 
> **入门**：若要开始使用模板，请参阅[创作 Azure 资源管理器模板](../../resource-group-authoring-templates.md)。
