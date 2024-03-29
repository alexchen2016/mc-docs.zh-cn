---
title: 诊断概述 - Azure 应用服务
description: 了解如何使用应用服务诊断排查 Web 应用的问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/10/2017
ms.date: 06/10/2019
ms.author: v-biyu
ms.openlocfilehash: f71388d2f1e10aa131cd7ed510826fb02b995ae0
ms.sourcegitcommit: df835d7fa96d783060311bf7c1dbffb10571bcfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66296700"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是智能的交互式体验，可帮助你排查应用的问题，且无需配置。 如果应用确实出现问题，应用服务诊断会指出问题所在，并引导你获取适当的信息，以便更轻松快速地排查和解决问题。

尽管此体验在应用过去 24 小时内出现问题时可发挥最大的作用，但是，你始终可以使用所有诊断图形进行分析。



## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

若要访问应用服务诊断，请在 [Azure 门户](https://portal.azure.cn)中导航到自己的应用服务 Web 应用。 

对于 Azure Functions，请导航到你的函数应用，在顶部的导航栏中，单击“平台功能”  并从“监视”部分中选择“诊断并解决问题”。   

![主页](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>运行状况检查

如果不知道 Web 应用发生什么问题，或者不知道从何处着手排查问题，运行状况检查是一个很好的入手点。 运行状况检查将会分析 Web 应用程序，提供简明的交互式概述，指出哪个组件正常、哪个组件出错，并告知要从哪个位置调查问题。 使用其智能交互式界面可逐步完成故障排除的过程。  

![运行状况检查](./media/app-service-diagnostics/HealthCheckup2.png)

如果在过去 24 小时内检测到属于特定问题类别的问题，可以查看完整的诊断报告。应用服务诊断可以通过引导性更强的体验提示查看更多的故障排除建议和后续措施。

![故障排除和后续措施](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>磁贴快捷方式

如果你确切地知道需要查找哪种类型的故障排除信息，磁贴快捷方式会将你直接转到所需问题类别的完整诊断报告。 与运行状况检查相比，磁贴快捷方式更直接，但访问诊断指标时，其引导性较差。 作为磁贴快捷方式的一部分，还可以在此处找到**诊断工具**，它们是更高级的工具，可帮助调查与应用程序代码问题、速度缓慢、连接字符串以及其他内容相关的问题。 

![磁贴快捷方式](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>诊断报告

无论是在运行[运行状况检查](#health-checkup)之后想要查看详细信息，还是单击了某个[磁贴快捷方式](#tile-shortcuts)，完整诊断报告都会显示过去 24 小时内的相关图形化指标。 如果应用出现任何停机，时间线下面将以橙色条形的方式呈现此故障。 可以选择橙色条形之一来选择故障时间以查看与该故障时间相关的观测数据以及建议的故障排除步骤。 

![诊断报告](./media/app-service-diagnostics/DiagnosticReport5.png)

