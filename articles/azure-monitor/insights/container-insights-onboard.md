---
title: 如何载入用于容器的 Azure Monitor | Azure Docs
description: 本文介绍如何为容器安装和配置 Azure Monitor，以便了解容器的性能以及已识别的性能相关问题。
services: azure-monitor
documentationcenter: ''
author: lingliw
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/19
ms.author: v-lingwu
ms.openlocfilehash: ae8b0c54fbb2ef03448a97dc309a24fecfdf58f4
ms.sourcegitcommit: f818003595bd7a6aa66b0d3e1e0e92e79b059868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66731204"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>如何为容器载入 Azure Monitor  
本文介绍如何为容器设置 Azure Monitor，以监视部署到 Kubernetes 环境和在 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/)中托管的工作负荷的性能。

可使用以下支持的方法为 AKS 的新部署，或是一个或多个现有部署启用适用于容器的 Azure Monitor：

* 借助 Azure 门户、Azure PowerShell 或 Azure CLI


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件 
在开始之前，请确保做好以下准备：

- **Log Analytics 工作区。** 可以在对新 AKS 群集启用监视时创建该工作区，或者让载入体验在 AKS 群集订阅的默认资源组中创建默认的工作区。 如果选择自行创建工作区，可以通过 [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md) 或在 [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)来创建。
- 需要成为 **Log Analytics 参与者角色**的成员才能启用容器监视。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。
- 在 AKS 群集资源上，你是 **[所有者](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-role.mds#owner)** 角色的成员。 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>组件 

监视性能的能力依赖于 Linux 容器化 Log Analytics 代理，该代理专门针对用于容器的 Azure Monitor 开发而来。 此专用代理可从群集中的所有节点处收集性能和事件数据，并且在部署期间，会自动部署该代理，并注册指定 Log Analytics 工作区。 该代理的版本为 microsoft/oms:ciprod04202018 或更高版本，并由采用以下格式的日期表示：mmddyyyy  。 

当该代理的新版本发布时，会在承载于 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集上自动升级该代理。 要关注发布的版本，请参阅[代理发布公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。 

>[!NOTE] 
>如果已部署 AKS 群集，可使用 Azure CLI 或提供的 Azure 资源管理器模板启用监视，如后文所示。 不能使用 `kubectl` 升级、删除、重新部署或部署代理。 模板需要部署在群集所在的资源组中。”

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.cn)。 

## <a name="enable-monitoring-for-a-new-cluster"></a>启用对新群集的监视
在部署期间，可以在 Azure 门户中、通过 Azure CLI 或 Terraform 对新 AKS 群集启用监控。  

>[!NOTE]
>按快速入门文章中的步骤操作，以便通过门户启用对 AKS 群集的监视时，系统会提示你选择一个现有的 Log Analytics 工作区，或者创建一个新的。 

### <a name="enable-using-azure-cli"></a>启用 Azure CLI
若要使用 Azure CLI 对新建的 AKS 群集启用监视，请遵循快速入门文章的[创建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)部分中所述的步骤。  

>[!NOTE]
>如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.43 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。 
>

### <a name="enable-using-terraform"></a>使用 Terraform
如果正在使用 Terraform 部署新的 AKS 群集，请指定配置文件所需的参数，以便在未指定现有工作区时[创建 Log Analytics 工作区](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html)。 

>[!NOTE]
>如果选择使用 Terraform，则必须运行 Terraform Azure RM 提供程序 1.17.0 版或更高版本。

要将用于容器的 Azure Monitor 添加到工作区，请参阅 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)，并通过纳入 [addon_profile](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile)，以及指定 oms_agent，完成配置文件   。 

启用监视并成功完成所有配置任务后，可通过两种方法监视群集性能：

* 直接在 AKS 群集中从左侧窗格选择“运行状况”  。
* 在选定群集的 AKS 群集页面中选择“监视容器见解”磁贴  。 在 Azure Monitor 中，从左侧窗格选择“运行状况”  。 

  ![用于在 AKS 中为容器选择 Azure Monitor 的选项](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>启用对现有托管群集的监视
可以使用下述支持的方法之一，以便启用对已部署的 AKS 群集的监视：

* Azure CLI
* Terraform
* [使用 Azure Monitor](#enable-from-azure-monitor-in-the-portal)，或者在 Azure 门户中[直接使用 AKS 群集](#enable-directly-from-aks-cluster-in-the-portal) 
* 通过 Azure PowerShell cmdlet `New-AzResourceGroupDeployment` 来使用[提供的 Azure 资源管理器模板](#enable-using-an-azure-resource-manager-template)，或者使用 Azure CLI。 

### <a name="enable-using-azure-cli"></a>启用 Azure CLI
以下步骤使用 Azure CLI 对 AKS 群集启用监视。 在此示例中，不需要预先创建或指定现有的工作区。 如果区域中尚不存在默认的工作区，此命令可以简化在 AKS 群集订阅的默认资源组中创建默认工作区的过程。  创建的默认工作区的格式类似于 *DefaultWorkspace-\<GUID>-\<Region>* 。  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

输出如下所示：

```azurecli
provisioningState       : Succeeded
```

如果希望与现有工作区集成，请使用以下命令指定该工作区。

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

输出如下所示：

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-using-terraform"></a>使用 Terraform
1. 将 oms_agent 附加配置文件添加到现有 [azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) 资源 

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. 按照 Terraform 文档中的步骤添加 [ azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)。

### <a name="enable-from-azure-monitor-in-the-portal"></a>在门户中通过 Azure Monitor 来启用 
要启用 Azure Monitor 对 Azure 门户中的 AKS 群集的监视，请执行以下操作：

1. 在 Azure 门户中选择“监视”。  
2. 从列表中选择容器  。
3. 在“监视 - 容器”页上，选择“非监视群集”   。
4. 从非监视群集的列表中找到容器，然后单击“启用”  。   
5. 在“载入到用于容器的 Azure Monitor”页上，如果现有 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区  。  
    列表预先选择了 AKS 容器在订阅中部署到的默认工作区和位置。 

    ![启用 AKS 容器见解监视](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果想要创建新的 Log Analytics 工作区用于存储来自群集的监视数据，请按照[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)中的说明进行操作。 确保在部署 AKS 容器的同一订阅中创建工作区。 
 
启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>在门户中直接使用 AKS 群集来启用
若要在 Azure 门户中直接使用某个 AKS 群集来启用监视，请执行以下操作：

1. 在 Azure 门户中，选择“所有服务”。  
2. 在资源列表中，开始键入“Containers”  。  
    列表会根据输入的内容进行筛选。 
3. 选择“Kubernetes 服务”  。  

    ![Kubernetes 服务链接](./media/container-insights-onboard/portal-search-containers-01.png)

4. 在容器列表中，选择一个容器。
5. 在容器概述页面中，选择“监视容器”  。  
6. 在“载入到用于容器的 Azure Monitor”页上，如果现有 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区  。  
    列表预先选择了 AKS 容器在订阅中部署到的默认工作区和位置。 

    ![启用 AKS 容器运行状况监视](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >如果想要创建新的 Log Analytics 工作区用于存储来自群集的监视数据，请按照[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)中的说明进行操作。 确保在部署 AKS 容器的同一订阅中创建工作区。 
 
启用监视后，可能需要约 15 分钟才能查看群集的运行数据。 

### <a name="enable-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板来启用
此方法包含两个 JSON 模板。 一个模板指定用于启用监视的配置，另一个模板包含参数值，通过配置这些参数值可指定：

* AKS 容器资源 ID。 
* 在其中部署群集的资源组。
* Log Analytics 工作区和要在其中创建工作区的区域。 

>[!NOTE]
>模板需要部署在群集所在的资源组中。
>

必须创建 Log Analytics 工作区，然后才能使用 Azure PowerShell 或 CLI 来启用监视。 若要创建工作区，可通过 [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md) 或在 [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)中进行设置。

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.59 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。 

#### <a name="create-and-execute-a-template"></a>创建和执行模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. 将此文件以“existingClusterOnboarding.json”文件名保存到本地文件夹  。
3. 将以下 JSON 语法粘贴到文件中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. 使用 AKS 群集的“AKS 概述”页面中的值，编辑 **aksResourceId** 和 **aksResourceLocation** 的值  。 **workspaceResourceId** 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。 
5. 将此文件以“existingClusterParam.json”文件名保存到本地文件夹  。
6. 已做好部署此模板的准备。 

   * 若要使用 Azure PowerShell 进行部署，请在包含模板的文件夹中使用以下命令：

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```powershell
       provisioningState       : Succeeded
       ```

   * 若要使用 Azure CLI 进行部署，请运行下列命令：
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```azurecli
       provisioningState       : Succeeded
       ```
     启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。 

## <a name="verify-agent-and-solution-deployment"></a>验证代理和解决方案部署
如果代理版本为 *06072018* 或更高版本，则可验证代理和解决方案是否均已成功部署。 如果是早期版本的代理，则只能验证代理的部署情况。

### <a name="agent-version-06072018-or-later"></a>06072018 版或更高版本的代理
运行以下命令，验证代理是否已成功部署。 

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，指明其已正确部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要验证解决方案的部署，请运行以下命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

输出应如下所示，指明其已正确部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>代理版本低于 06072018

若要验证 06072018  之前发布的 Log Analytics 代理版本是否已正确部署，请运行以下命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，指明其已正确部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 查看配置
使用 `aks show` 命令获取详细信息，例如，解决方案是否已启用、Log Analytics 工作区 resourceID 是什么，以及有关群集的摘要详细信息。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

片刻之后，该命令将会完成，并返回有关解决方案的 JSON 格式信息。  命令结果应显示监视加载项配置文件，并类似于以下示例输出：

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>后续步骤

* 如果在尝试载入解决方案时遇到问题，请查看[故障排除指南](container-insights-troubleshoot.md)

* 启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。




