---
title: 带有 IoT 远程监视的设备模拟 - Azure | Microsoft Docs
description: 本操作说明指南介绍如何在远程监视解决方案加速器中使用设备模拟器。
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 09/28/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: 8eb235db3cc2b0d5226abb0df0c48972bb66a745
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52661314"
---
# <a name="create-and-test-a-new-simulated-device"></a>创建并测试新的模拟设备

借助远程监视解决方案加速器，可以定义自己的模拟设备。 本文介绍如何定义新的模拟灯泡设备，然后在本地对其进行测试。 解决方案加速器包括诸如冷却器和卡车的模拟设备。 但是，可以定义自己的模拟设备以在部署实际设备之前对 IoT 解决方案进行测试。

本操作说明指南介绍如何自定义设备模拟微服务。 此微服务是远程监视解决方案加速器的一部分。 为了演示设备模拟功能，本操作说明指南在 Contoso IoT 应用程序中使用了两个方案：

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>后续步骤

本指南介绍了如何创建自定义模拟设备类型，以及如何通过本地运行设备模拟微服务来对其进行测试。

建议的下一步是学习如何将自定义模拟设备类型部署到[远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy-simulated-device.md)。
