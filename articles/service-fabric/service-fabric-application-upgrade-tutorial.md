---
title: Service Fabric 应用升级教程 | Azure
description: 本文逐步指导使用 Visual Studio 部署 Service Fabric 应用程序、更改代码以及推出升级版本。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/23/2018
ms.date: 05/28/2018
ms.author: v-yeche
ms.openlocfilehash: 90afe688985f6ef96b3deaba585b9b2d273662b2
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627325"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>使用 Visual Studio 进行 Service Fabric 应用程序升级的教程
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric 确保只升级已更改的服务，并在整个升级过程中监视应用程序的运行状况，从而可以简化云应用程序的升级过程。 它还能在应用程序发生任何问题时自动回滚到旧版本。 Service Fabric 应用程序升级造成的 *停机时间为零*，因为可以在不停机的情况下升级应用程序。 本教程介绍如何从 Visual Studio 完成滚动升级。

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>步骤 1：构建和发布视觉对象示例
首先，从 GitHub 下载[可视对象](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects)应用程序。 然后，右键单击应用程序项目 **VisualObjects**，并从 Service Fabric 菜单项中选择“发布”命令生成并发布应用程序。

![Service Fabric 应用程序的上下文菜单][image1]

选择“发布”会显示一个弹出窗口，可以将“目标配置文件”设置为 **PublishProfiles\Local.xml**。 在单击“**发布**”之前，该窗口应如下所示。

![发布 Service Fabric 应用程序][image2]

现在，可在对话框中单击“**发布**”。 可以使用 [Service Fabric Explorer 查看群集和应用程序](service-fabric-visualizing-your-cluster.md)。 “视觉对象”应用程序有一个 Web 服务，在浏览器的地址栏中输入 [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) 即可转到该服务。  应会在屏幕上看到 10 个四处移动的浮动可视对象。

**注意：** 如果部署到 `Cloud.xml` 配置文件 (Azure Service Fabric)，则可以在 <strong>http://{ServiceFabricName}.{Region}.cloudapp.chinacloudapi.cn:8081/visualobjects/</strong> 访问应用程序。 请确保在负载均衡器中配置了 `8081/TCP`（在 Service Fabric 实例所在的资源组中找到负载均衡器）。

## <a name="step-2-update-the-visual-objects-sample"></a>步骤 2：更新视觉对象示例
你可能会注意到，使用步骤 1 中部署的版本，视觉对象不会旋转。 让我们将此应用程序升级到视觉对象也会旋转的版本。

选择 VisualObjects 解决方案中的 VisualObjects.ActorService 项目，并打开 **VisualObjectActor.cs** 文件。 在该文件中，转到 `MoveObject` 方法，注释掉 `visualObject.Move(false)`，并取消注释 `visualObject.Move(true)`。 此代码更改可在升级服务后旋转对象。  **现在可以生成（不是重新生成）解决方案**，这会生成修改后的项目。 如果选择“*全部重新生成*”，则必须更新所有项目的版本。

我们还需要设置应用程序的版本。 若要在右键单击“**VisualObjects**”项目之后进行版本更改，可以使用 Visual Studio 的“**编辑清单版本**”选项。 选择此选项会显示用于编辑版本的对话框，如下所示：

![版本控制对话框][image3]

将已修改的项目及其代码包连同应用程序的版本一起更新为 2.0.0。 完成更改后，清单应该如下所示（粗体部分即为所做的更改）：

![更新版本][image4]

如果选择“**自动更新应用程序和服务版本**”，Visual Studio 工具可以自动滚动更新版本。 如果使用 [SemVer](http://www.semver.org)，则在选择该选项后，需要单独更新代码和/或配置包版本。

保存更改，并选中“**升级应用程序**”框。

## <a name="step-3--upgrade-your-application"></a>步骤 3：升级应用程序
请熟悉[应用程序升级参数](service-fabric-application-upgrade-parameters.md)和[升级过程](service-fabric-application-upgrade.md)，充分了解可应用的各种升级参数、超时和运行状况标准。 在本演练中，服务运行状况评估条件设置为默认值（不受监视模式）。 如果要配置这些设置，可以选择“**配置升级设置**”，并视需要修改参数。

现在，我们已经准备好选择“**发布**”来开始升级应用程序。 此选项会将应用程序升级到对象会旋转的版本 2.0.0。 Service Fabric 每次会升级一个更新域（先更新一些对象，再更新另一些对象），在升级期间，仍可访问服务。 可以通过客户端（浏览器）检查是否可以访问服务。  

现在，随着应用程序不断升级，可以使用 Service Fabric Explorer 来监视应用程序（使用应用程序下的“**正在进行升级**”选项卡）。

几分钟后，所有更新域应已升级（已完成），Visual Studio 输出窗口应该也会指出升级已完成。 此外，可以看到，浏览器窗口中的*所有* 可视对象都在旋转！

可以尝试通过更改版本来练习本文所述的操作：从版本 2.0.0 升级到版本 3.0.0，或者从版本 2.0.0 降级到版本 1.0.0。 尝试练习使用超时和运行状况策略，以便加深对它的熟悉。 与部署到本地群集不同，在部署到 Azure 群集时，可能需要使用不同的参数。 们建议保守设置超时值。

## <a name="next-steps"></a>后续步骤
[使用 PowerShell 升级应用程序](service-fabric-application-upgrade-tutorial-powershell.md)逐步讲解了如何使用 PowerShell 进行应用程序升级。

使用[升级参数](service-fabric-application-upgrade-parameters.md)来控制应用程序的升级方式。

了解如何使用[数据序列化](service-fabric-application-upgrade-data-serialization.md)，使应用程序在升级后保持兼容。

参考[高级主题](service-fabric-application-upgrade-advanced.md)，了解如何在升级应用程序时使用高级功能。

参考[对应用程序升级进行故障排除](service-fabric-application-upgrade-troubleshooting.md)中的步骤来解决应用程序升级时的常见问题。

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!--Update_Description: update meta properties  -->