---
title: 使用 PowerShell 升级 Service Fabric 应用 | Azure
description: 本文逐步指导使用 PowerShell 部署 Service Fabric 应用程序、更改代码以及推出升级版本。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/23/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: b5ab0c27002d2de8ad30505dedc330eaf411fc01
ms.sourcegitcommit: ea33f8dbf7f9e6ac90d328dcd8fb796241f23ff7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57204147"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>使用 PowerShell 升级 Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

最常用的推荐升级方法是受监控的滚动升级。  Azure Service Fabric 基于一组运行状况策略监视正在升级的应用程序的运行状况。 升级某个更新域 (UD) 之后，Service Fabric 将评估应用程序运行状况，根据运行状况策略继续升级下一个更新域，或者使升级失败。

可使用托管或本机 API、PowerShell、Azure CLI、Java 或 REST 执行受监视的应用程序升级。 有关使用 Visual Studio 执行升级的说明，请参阅[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)。

使用 Service Fabric 监视的滚动升级，应用程序管理员可以配置 Service Fabric 用于确定应用程序运行状况是否正常的运行状况评估策略。 此外，管理员还可以配置当运行状况评估失败时要采取的措施（例如，执行自动回滚）。本部分演练使用 PowerShell 对其中一个 SDK 示例进行受监视的升级。

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>步骤 1：构建和部署 Visual Objects 示例
单击右键应用程序项目 **VisualObjectsApplication**，并选择“**发布**”命令生成并发布应用程序。  有关详细信息，请参阅 [Service Fabric 应用程序升级教程](service-fabric-application-upgrade-tutorial.md)。  或者，也可以使用 PowerShell 来部署应用程序。

> [!NOTE]
> 要在 PowerShell 中使用任何 Service Fabric 命令，必须先使用 `Connect-ServiceFabricCluster` cmdlet 连接到群集。 同样，假设已在本地计算机上设置了群集。 请参阅[设置 Service Fabric 部署环境](service-fabric-get-started.md)上的文章。
> 
> 

在 Visual Studio 中构建项目后，可以使用 PowerShell 命令 [Copy-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricapplicationpackage) 将应用程序包复制到 ImageStore。 如果要在本地验证应用包，请使用 [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet。 下一个步骤是使用 [Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet 将应用程序注册到 Service Fabric 运行时。 接下来的一步骤是使用 [New-ServiceFabricApplication](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 启动应用程序实例。  这三个步骤类似于使用 Visual Studio 中的“**部署**”菜单项。  完成预配后，应从映像存储区中清除复制的应用程序包以减少占用的资源。  如果不再需要某一应用程序类型，它应出于同样的原因将其注销。 有关详细信息，请按照[使用 PowerShell 部署和删除应用程序](service-fabric-application-upgrade-tutorial-powershell.md)。

现在可以使用 [Service Fabric Explorer 查看群集和应用程序](service-fabric-visualizing-your-cluster.md)。 应用程序具有一个 Web 服务，可通过在 Internet Explorer 地址栏中键入 [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) 导航到该 Web 服务。  应在屏幕上看到一些四处移动的浮动视觉对象。  此外，可使用 [Get-ServiceFabricApplication](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) 检查应用程序状态。

## <a name="step-2-update-the-visual-objects-sample"></a>步骤 2：更新视觉对象示例
你可能会注意到，使用步骤 1 中部署的版本，视觉对象不会旋转。 让我们将此应用程序升级到视觉对象也会旋转的版本。

选择 VisualObjects 解决方案中的 VisualObjects.ActorService 项目，并打开该项目中的 StatefulVisualObjectActor.cs 文件。 在该文件内，导航到 `MoveObject` 方法，注释掉 `this.State.Move()`，并取消注释 `this.State.Move(true)`。 此项更改可在升级服务后旋转对象。

我们还需要更新 **VisualObjects.ActorService** 项目的 *ServiceManifest.xml* 文件（位于 PackageRoot 下）。 将 *CodePackage* 和服务版本更新到 2.0，以及 *ServiceManifest.xml* 文件中的相应行。
可以在右键单击解决方案之后，使用 Visual Studio 中的“*编辑清单文件*”选项来进行清单文件更改。

完成更改后，清单应该如下所示（突出显示的部分即为所做的更改）：

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

现在，*ApplicationManifest.xml* 文件（位于 **VisualObjects** 解决方案下的 **VisualObjects** 项目下）已更新为 **VisualObjects.ActorService** 项目的 2.0 版。 此外，应用程序版本已从 1.0.0.0 更新为 2.0.0.0。 *ApplicationManifest.xml* 应类似于以下代码片段：

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

现在请生成项目，方法是选择 **ActorService** 项目，并单击右键并选择 Visual Studio 中的“**生成**”选项。 如果选择“**全部重新生成**”，应该更新所有项目的版本，因为代码已更改。 接下来，将更新的应用程序打包，方法是右键单击“***VisualObjectsApplication***”，选择 Service Fabric 菜单，并选择“**打包**”。 此操作创建可部署的应用程序包。  更新的应用程序已准备就绪，可供部署。

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>步骤 3：确定运行状况策略和升级参数
请熟悉[应用程序升级参数](service-fabric-application-upgrade-parameters.md)和[升级过程](service-fabric-application-upgrade.md)，充分了解所应用的各种升级参数、超时和运行状况条件。 对于本演练，服务运行状况评估条件设置为默认值（即推荐值），这意味着在升级后所有服务和实例均应为*运行状况正常*。  

但是，让我们将 *HealthCheckStableDuration* 增加到 180 秒（这样该服务在进行下一个更新域的升级之前将至少保持 120 秒的运行状况正常）。  我们还将 *UpgradeDomainTimeout* 设置为 1200 秒，将 *UpgradeTimeout* 设置为 3000 秒。

最后，将 *UpgradeFailureAction* 设置为 rollback。 此选项要求 Service Fabric 在升级期间遇到任何问题时，将应用程序回退到前一版本。 因此在开始升级（步骤 4）时指定了以下参数：

FailureAction = Rollback

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>步骤 4：为升级准备应用程序
现在，应用程序已生成并准备好进行升级。 如果以管理员身份打开 PowerShell 窗口并键入 [Get-ServiceFabricApplication](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)，会看到已部署好的 1.0.0.0 应用程序类型的名为 **VisualObjects** 的应用程序。  

应用程序包存储在解压 Service Fabric SDK 的位置，其相对路径为 *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*。 该目录中应会出现一个“Package”文件夹，这是存储应用程序包的位置。 请检查时间戳，确保它是最新版本（可能还需要相应地修改路径）。

现在，让我们将更新后的应用程序包复制到 Service Fabric ImageStore（Service Fabric 存储应用程序包的位置）。 参数 *ApplicationPackagePathInImageStore* 告知 Service Fabric 可在何处找到应用程序包。 我们可以使用以下命令将更新的应用程序放入“VisualObjects\_V2”（可能需要再次相应地修改路径）。

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

下一步是将此应用程序注册到 Service Fabric，这可以使用 [Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 命令来执行：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

如果上面的命令未成功，可能需要重新生成所有服务。 如步骤 2 中所述，可能还需要更新 WebService 版本。

建议在成功注册应用程序后删除应用程序包。  从映像存储区中删除应用程序包可以释放系统资源。  保留未使用的应用程序包会占用磁盘存储空间，导致应用程序出现性能问题。

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>步骤 5：启动应用程序升级
现在可以使用 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) 命令开始升级应用程序：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```

应用程序名称与 *ApplicationManifest.xml* 文件中所述的相同。 Service Fabric 使用此名称来确定升级的应用程序。 如果设置的超时太短，则可能遇到一条说明该问题的失败消息。 请参阅故障排除部分，或增加超时值。

现在，在应用程序升级进行时，可以使用 Service Fabric Explorer 或 PowerShell 命令 [Get-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) 对其进行监视： 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

几分钟后，使用上述 PowerShell 命令获得的状态应说明所有更新域均已升级（已完成）。 此外，浏览器窗口中的视觉对象已开始旋转！

可以练习从版本 2 升级到版本 3，或者从版本 2 升级到版本 1。 从版本 2 过渡到版本 1 也被视为升级。 尝试练习使用超时和运行状况策略，以便加深对它的熟悉。 部署到 Azure 群集时，需要正确设置参数。 最好保守地设置超时。

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)逐步讲解了如何使用 Visual Studio 进行应用程序升级。

使用[升级参数](service-fabric-application-upgrade-parameters.md)来控制应用程序的升级方式。

了解如何使用[数据序列化](service-fabric-application-upgrade-data-serialization.md)，使应用程序在升级后保持兼容。

参考[高级主题](service-fabric-application-upgrade-advanced.md)，了解如何在升级应用程序时使用高级功能。

参考[对应用程序升级进行故障排除](service-fabric-application-upgrade-troubleshooting.md)中的步骤来解决应用程序升级时的常见问题。

<!--Update_Description: update meta properties, wording update, update link-->