---
title: 预测性维护解决方案加速器概述 - Azure | Microsoft Docs
description: Azure IoT 预测性维护解决方案加速器概述。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
origin.date: 10/26/2018
ms.date: 12/17/2018
ms.author: v-yiso
ms.openlocfilehash: 0244963132905103e12fc45832d930b78b6fb3cb
ms.sourcegitcommit: b64a6decfbb33d82a8d7ff9525726c90f3540d4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53569239"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>预测性维护解决方案加速器概述


预测性维护解决方案加速器是一个用于商业应用场景的端到端解决方案，可预测可能发生故障的时间点。 可以主动对优化维护等活动运用此解决方案加速器。 该解决方案结合了重要的 Azure IoT 解决方案加速器服务，例如 IoT 中心。  此工作区包含基于公用示例数据集的模型，用于预测飞机引擎的剩余使用寿命 (RUL)。 此解决方案全面实施 loT 商业应用场景，以此为起点，可以规划和实施满足自己特定业务需求的解决方案。

## <a name="logical-architecture"></a>逻辑体系结构

下图概述该解决方案加速器的逻辑组件：

![逻辑体系结构][img-architecture]

蓝色项是在部署解决方案加速器时，在同一区域中预配的 Azure 服务。 [预配页][lnk-azureiotsuite]显示了可部署解决方案加速器的区域列表。

绿色项是模拟的飞机引擎。 可以在[模拟设备](#simulated-devices)部分了解有关这些模拟设备的详细信息。

灰色项是实现*设备管理*功能的组件。 当前的预测性维护解决方案加速器版本不会预配这些资源。 若要了解有关设备管理的详细信息，请参阅[远程监视解决方案加速器][lnk-remote-monitoring]。

## <a name="simulated-devices"></a>模拟设备

在该解决方案加速器中，模拟设备是飞机引擎。 该解决方案预配有两个映射到单架飞机的引擎。 每个引擎发出 4 种遥测数据：传感器 9、传感器 11、传感器 14 和传感器 15 为 R 模型提供所需的数据来计算引擎的 RUL。 每个模拟设备会将下列遥测消息发送到 IoT 中心：

*周期计数*。 一个周期是 2 到 10 小时不等的已完成飞行。 在飞行期间，每半小时捕获一次遥测数据。

*遥测*。 有四个对引擎属性进行记录的传感器。 这些传感器一般标记为传感器 9、传感器 11、传感器 14 和传感器 15。 这四个传感器发送足以从 RUL 模型获取有用结果的遥测数据。 解决方案加速器中使用的模型是基于包含实际引擎传感器数据的公共数据集创建的。 有关如何根据原始数据集创建该模型的详细信息，请参阅 [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics]（Cortana Intelligence 库预见性维护模板）。

模拟设备可以处理在解决方案中通过 IoT 中心发送的以下命令：

| 命令 | 说明 |
| --- | --- |
| StartTelemetry |控制模拟的状态。<br/>使设备开始发送遥测 |
| StopTelemetry |控制模拟的状态。<br/>使设备停止发送遥测 |

IoT 中心会提供设备命令确认。

## <a name="azure-stream-analytics-job"></a>Azure 流分析作业

**作业：遥测**会使用两个语句来操作传入设备遥测流：

* 第一个语句会从设备选择所有遥测，然后将这些数据 从该存储中，数据在 Web 应用中进行可视化。
* 第二个语句会通过两分钟的滑动窗口计算平均传感器值，然后通过事件中心将这些数据发送到事件处理器。

## <a name="event-processor"></a>事件处理器
**事件处理器主机** 在 Azure Web 作业中运行。 **事件处理器** 获取已完成周期的平均传感器值。 然后，它将这些值传递给用于计算引擎 RUL 的已训练模型。 API 提供对属于解决方案一部分的机器学习工作区中的模型的访问。

## <a name="r-server"></a>R Server
R Server 实现使用派生自数据的模型，这些数据是从实际飞机引擎收集的。 若要访问 R Server 和模型，请参阅 [Microsoft Azure IoT 套件][lnk-azureiotsuite]中预配解决方案的解决方案面板上的链接。

Azure IoT 预测性维护解决方案加速器使用通过此模板创建的回归模型。 该模型将部署到你的 Azure 订阅，并且将通过一个自动生成的 API 使其可用。 该解决方案包含代表 4 个（共 100 个）引擎和 4 个（共 21 个）传感器数据流的测试数据的子集。 该数据已足以通过训练模型提供精确的结果。

*\[1\] A. Saxena 和 K. Goebel (2008)。"Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>后续步骤
了解预测性维护解决方案加速器的关键组件后，可对其进行自定义。

还可以探索 IoT 解决方案加速器的一些其他功能：

* [IoT 解决方案加速器的常见问题解答][lnk-faq]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.cn/
[lnk-faq]: iot-accelerators-faq.md
