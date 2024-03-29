---
title: 远程监视解决方案体系结构选择 - Azure | Microsoft Docs
description: 本文介绍在远程监视中所做的体系结构和技术选择
author: timlaverty
manager: camerons
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 09/12/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: a7cabec3fa8bdbee2498013f37b36c7322d375f9
ms.sourcegitcommit: b64a6decfbb33d82a8d7ff9525726c90f3540d4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53569341"
---
# <a name="remote-monitoring-architectural-choices"></a>远程监视体系结构选择

Azure IoT 远程监视解决方案加速器是 MIT 许可的开放源代码解决方案加速器。 该加速器提供常见 IoT 方案以帮助你加快 IoT 开发流程，例如：

- 设备连接
- 设备管理
- 流处理

远程监视解决方案遵循建议的 [Azure IoT 参考体系结构](https://aka.ms/iotrefarchitecture)。

本文介绍了每个远程监视子系统中所做的体系结构和技术选择以及所考虑的备选方案。 但是，Microsoft 在远程监视解决方案中所做的技术选择不是实现远程监视 IoT 解决方案的唯一方法。 应将技术实现视为生成成功的应用程序的基准且应对其进行修改，以便：

- 适应组织中的可用技能和经验。
- 满足你的垂直应用程序需求。

## <a name="architectural-choices"></a>体系结构选择

Microsoft 建议用于 IoT 应用程序的体系结构的基础是云原生、微服务和无服务器。 应将 IoT 应用程序的不同子系统构建为可以独立部署和缩放的离散式服务。 有了这些属性，就可以提高规模，在更新单个子系统时更加灵活，并可为每个子系统灵活地选择适当的技术。

可以使用多项技术实现微服务。 例如，可以选择以下任一选项来实现微服务：

- 将 Docker 等容器技术和 Azure Functions 等无服务器技术一起使用。
- 在 Azure 应用服务等 PaaS 服务中托管微服务。

## <a name="technology-choices"></a>技术选择

此部分详述在远程监视解决方案中针对每个核心子系统所做的技术选择。

![核心关系图](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>云网关

将 Azure IoT 中心用作远程监视解决方案云网关。 [IoT 中心](/iot-hub/)提供与设备进行的安全、双向通信。

对 IoT 设备进行连接，可以使用：

- [IoT 中心设备 SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-device-sdks) 为设备实现本机客户端应用程序。 这些 SDK 提供 IoT 中心 REST API 的包装器，并处理包括重试在内的各种方案。
- 在解决方案加速器中与 Azure IoT Edge 集成，以部署和管理在设备上的容器中运行的自定义模块。

### <a name="stream-processing"></a>流处理

对于流式处理，远程监视解决方案使用 Azure 流分析进行复杂的规则处理。 如果想使用更简单的规则，则可以使用支持简单规则处理的自定义微服务，虽然此设置不包含在现成的部署中。 参考体系结构建议将 Azure Functions 用于简单的规则处理，将 Azure 流分析用于复杂的规则处理。

### <a name="storage"></a>存储

远程监视解决方案加速器在存储方面使用 Azure Cosmos DB。 解决方案加速器将 Azure Cosmos DB 用于所有其他存储，例如冷存储、规则定义、警报和配置设置。

Azure Cosmos DB 是建议用于 IoT 应用程序的常规用途暖存储解决方案。 此功能可以进行根本原因分析并避免成本高昂的故障时间。

> [!NOTE]
> 时序见解目前在 Azure 中国云中不可用。 Azure 中国云中的新远程监视解决方案加速器部署使用 Cosmos DB 进行所有存储。

### <a name="business-integration"></a>业务集成

远程监视解决方案中的业务集成仅限于放置在暖存储中的警报的生成。 将解决方案与 Azure 逻辑应用连接，以实现更深入的业务集成方案。

### <a name="user-interface"></a>用户界面
此 Web UI 是使用 JavaScript React 生成的。  React 提供常用的行业 Web UI 框架，类似于其他常用的框架（例如 Angular）。  

### <a name="runtime-and-orchestration"></a>运行时和业务流程

远程监视解决方案使用 Docker 容器运行子系统，其中 Kubernetes 充当进行水平缩放所需的业务流程协调程序。 此体系结构为每个子系统启用单个规模定义。 但是，此体系结构确实会产生 DevOps 成本，这些成本主要体现在使虚拟机和容器保持最新且安全方面。

Docker 的替代方法包括在 Azure 应用服务等 PaaS 服务中托管微服务。 Kubernetes 的替代方法包括 Service Fabric、DC/OS 或 Swarm 等业务流程协调程序。

## <a name="next-steps"></a>后续步骤
* [在此处](https://www.azureiotsuite.cn/)部署远程监视解决方案。
* 浏览 [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) 和 [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/) 格式的 GitHub 代码。  
* 在[此处](https://aka.ms/iotrefarchitecture)详细了解 IoT 参考体系结构。
