---
title: 配置 Service Fabric 应用程序的升级 | Azure
description: 了解如何使用 Microsoft Visual Studio 来配置 Service Fabric 应用程序的升级设置。
services: service-fabric
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
origin.date: 06/29/2017
ms.date: 09/10/2018
ms.author: v-yeche
ms.openlocfilehash: 6255881caaa26b3832c793bd1ed066c425855cd7
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52664075"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>在 Visual Studio 中配置 Service Fabric 应用程序的升级
Azure Service Fabric 的 Visual Studio 工具提供发布到本地或远程群集的升级支持。 在测试和调试期间将应用程序升级到较新的版本而不是替换应用程序的三种方案：

* 应用程序数据不在升级期间丢失。
* 可用性仍然很高，因此，如果有足够的服务实例分散到升级域，则在升级期间不有任何服务中断。
* 在应用程序进行升级时，可以对该应用程序运行测试。

## <a name="parameters-needed-to-upgrade"></a>升级所需的参数
可以选择的部署类型有两种：常规或升级。 常规部署会将群集上所有先前的部署信息和数据都清除，而升级部署则将其保留。 在 Visual Studio 中升级 Service Fabric应用程序时，需要提供应用程序升级参数和运行状况检查策略。 应用程序升级参数可帮助控制升级，而运行状况检查策略可确定升级是否成功。 有关详细信息，请参阅 [Service Fabric 应用程序升级：升级参数](service-fabric-application-upgrade-parameters.md)。

有三种升级模式：Monitored、UnmonitoredAuto 和 UnmonitoredManual。

* Monitored 升级自动执行升级和应用程序运行状况检查。
* UnmonitoredAuto 升级自动执行升级，但跳过应用程序运行状况检查。
* 执行 UnmonitoredManual 升级时，必须手动升级每个升级域。

每种升级模式都需要不同的参数集。 若要深入了解可用的升级选项，请参阅[应用程序升级参数](service-fabric-application-upgrade-parameters.md)。

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>在 Visual Studio 中升级 Service Fabric 应用程序
如果要使用 Visual Studio Service Fabric 工具升级 Service Fabric应用程序，可以选中“升级应用程序”复选框，将发布程序指定为升级而不是常规部署。

### <a name="to-configure-the-upgrade-parameters"></a>配置升级参数
1. 单击复选框旁边的“设置”按钮。 此时将显示“编辑升级参数”对话框。 “编辑升级参数”对话框支持 Monitored、UnmonitoredAuto 和 UnmonitoredManual 升级模式。
2. 选择想要使用的升级模式，并填写参数网格。

    每个参数都有默认值。 可选参数 DefaultServiceTypeHealthPolicy 采用哈希表输入。 下面是 *DefaultServiceTypeHealthPolicy* 的哈希表输入格式示例：

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

     

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    下面是一个真实示例：

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. 如果选择 UnmonitoredManual 升级模式，则必须手动启动 PowerShell 控制台才能继续并完成升级过程。 若要了解手动升级的工作方式，请参阅 [Service Fabric 应用程序升级：高级主题](service-fabric-application-upgrade-advanced.md)。

## <a name="upgrade-an-application-by-using-powershell"></a>使用 PowerShell 升级应用程序
可以使用 PowerShell cmdlet 来升级 Service Fabric 应用程序。 有关详细信息，请参阅 [Service Fabric 应用程序升级教程](service-fabric-application-upgrade-tutorial.md)和 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)。

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>在应用程序清单文件中指定运行状况状态检查策略
Service Fabric 应用程序中的每个服务可能有自身的运行状况策略参数，这些参数可重写默认值。 可以在应用程序清单文件中提供这些参数值。

以下示例演示如何对应用程序列表中的每个服务应用唯一的运行状况检查策略。

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>后续步骤
有关升级应用程序的详细信息，请参阅[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)。

<!--Update_Description: update meta properties, update link -->