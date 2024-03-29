---
title: 将 Azure 资源移到新的订阅或资源组 | Azure
description: 使用 Azure Resource Manager 将资源移到新的资源组或订阅。
services: azure-resource-manager
documentationcenter: ''
author: rockboyfor
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 04/25/2019
ms.date: 06/03/2019
ms.author: v-yeche
ms.openlocfilehash: 119f01d6dbfdd9ea9a163e3ecd40e38eda1ae5d1
ms.sourcegitcommit: d75eeed435fda6e7a2ec956d7c7a41aae079b37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195392"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>将资源移到新资源组或订阅中

本文说明了如何将 Azure 资源移动到另一 Azure 订阅，或移动到同一订阅下的另一资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。

在移动操作过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源，但并不意味着资源已被冻结。 例如，如果将 SQL Server 及其数据库移到新的资源组中，使用数据库的应用程序体验不到停机， 仍可读取和写入到数据库。

移动资源仅能够将其移动到新的资源组。 移动操作不能更改该资源的位置。 新的资源组可能有不同的位置，但这不会更改该资源的位置。

> [!NOTE]
> 本文介绍如何在现有 Azure 订阅之间移动资源。 如果确实想要升级 Azure 订阅（例如从免费切换到标准的预先支付套餐），则需要转换订阅。
> 
> * 如果无法转换订阅，请[创建 Azure 支持请求](https://support.azure.cn/zh-cn/support/support-azure/?l=zh-cn)。 选择“订阅管理”  作为问题类型。

<!-- Not Available on [Upgrade your Trial or Azure Imagine Azure subscription to Pay-As-You-Go](..//billing/billing-upgrade-azure-subscription.md)-->
<!-- Not Available on [Switch your Azure subscription to another offer](../billing/billing-how-to-switch-azure-offer.md) -->

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>何时致电 Azure 支持人员

可以通过本文中所述的自助服务操作移动大部分资源。 使用自助服务操作：

* 移动 Resource Manager 资源。
* 根据 [经典部署限制](#classic-deployment-limitations)移动经典资源。

有以下需要时，请联系[支持人员](https://support.azure.cn/zh-cn/support/support-azure/)：

* 将资源移到新的 Azure 帐户（和 Azure Active Directory 租户），并且对于上一部分中的说明需要帮助。
* 移动经典资源，但遇到限制问题。

## <a name="services-that-can-be-moved"></a>可以移动的服务

以下列表提供了可移动到新资源组和订阅的 Azure 服务的一般摘要。 对于支持移动的资源类型列表，请参阅[支持移动操作的资源](move-support-resources.md)。

* Analysis Services
* API 管理
* 应用服务应用（Web 应用）- 请参阅[应用服务限制](#app-service-limitations)
* 应用服务证书 - 请参阅[应用服务证书限制](#app-service-certificate-limitations)
* 自动化 - Runbook 必须与自动化帐户存在于同一资源组中。
    <!-- Not Available * Azure Active Directory B2C-->
* Azure Redis 缓存 - 如果 Azure Redis 缓存实例配置了虚拟网络，则实例无法被移动到其他订阅。 请参阅[虚拟网络限制](#virtual-networks-limitations)。
* Azure Cosmos DB   <!-- Not Available * Azure Data Explorer-->
    <!-- Not Available * Azure Database for MariaDB-->
* Azure Database for MySQL
* Azure Database for PostgreSQL   <!-- Not Available * Azure DevOps-->
    <!-- Not Available * Azure Maps-->
    <!-- Not Available * Azure Monitor logs-->
    <!-- Not Available * Azure Relay-->
* Azure Stack - 注册
* Batch   <!-- Not Available * BizTalk Services-->
    <!-- Not Available * Bot Service-->
* CDN
* 云服务 - 请参阅 [经典部署限制](#classic-deployment-limitations)
* 认知服务
* 容器注册表
* 内容审查器   <!-- Not Available * Cost Management-->
    <!-- Not Available * Customer Insights-->
    <!-- Not Available * Data Catalog-->
    <!-- Not Available * Data Factory-->
    <!-- Not Available * Data Lake Analytics-->
    <!-- Not Available * Data Lake Store-->
    <!-- Not Available * DNS-->
    <!-- Not Available * Event Grid-->
* 事件中心   <!-- Not Available * Front Door-->
* HDInsight 群集 - 请参阅 [HDInsight 限制](#hdinsight-limitations)
    <!-- Not Available * Iot Central-->
* IoT 中心
* Key Vault - 用于磁盘加密的 Key Vault 不能移动到同一订阅中的资源组，也不能跨订阅移动。
* 负载均衡器 - 可以移动基本 SKU 负载均衡器。 不能移动标准 SKU 负载均衡器。
* 逻辑应用   <!-- Not Available * Machine Learning - Machine Learning Studio web services can be moved to a resource group in the same subscription, but not a different subscription. Other Machine Learning resources can be moved across subscriptions.-->
    <!-- Not Available * Managed Disks - see [Virtual Machines limitations for constraints](#virtual-machines-limitations)-->
    <!-- Not Available * Managed Identity - user-assigned-->
* 媒体服务
* 监视器 - 确保移动到新订阅时，不会超出[订阅配额](../azure-subscription-service-limits.md#monitor-limits)
* 通知中心   <!-- Not Available * Operational Insights-->
    <!-- Not Available * Operations Management-->
* 门户仪表板
* Power BI - Power BI Embedded 和 Power BI 工作区集合
* 公共 IP - 可以移动基本 SKU 公共 IP。 不能移动标准 SKU 公共 IP。
    <!--MOONCAKE: Not Available on * Recovery Services vault - enroll in a [preview](#recovery-services-limitations)-->
    <!--Pending Verify on * SAP HANA on Azure-->
* 计划程序   <!-- Not Available * Search-->
* 服务总线
* Service Fabric   <!-- Not Available * Service Fabric Mesh-->
    <!-- Not Available * SignalR Service-->
* 存储 - 不同区域的存储帐户无法通过同一操作进行移动。 请改为对每个区域使用单独的操作。
* 存储（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 流分析 - 当流分析作业处于运行状态时，则无法进行移动。
* SQL 数据库服务器 - 数据库和服务器必须位于同一个资源组中。 移动 SQL 服务器时，也会移动其所有数据库。 此行为适用于 Azure SQL 数据库和 Azure SQL 数据仓库数据库。
    <!-- Not Available * Time Series Insights-->
* 流量管理器
* 虚拟机 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟机（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 虚拟机规模集 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟网络 - 请参阅[虚拟网络限制](#virtual-networks-limitations)
* VPN 网关

### <a name="services-that-cannot-be-moved"></a>无法移动的服务

以下列表提供了不能移动到新资源组和订阅的 Azure 服务的一般摘要。 有关更多详细信息，请参阅[资源的移动操作支持](move-support-resources.md)。

<!-- Not Available * AD Domain Services-->
* AD 混合运行状况服务
* 应用程序网关   <!-- Not Available * Azure Database Migration-->
    <!-- Not Available * Azure Databricks-->
    <!-- Not Available * Azure Firewall-->
* Azure Kubernetes 服务 (AKS)   <!-- Not Available * Azure Migrate-->
    <!-- Not Available * Azure NetApp Files-->
* 证书 - 应用服务证书可以移动，但上传的证书存在[限制](#app-service-limitations)。
* 经典应用程序   <!-- Not Available * Container Instances-->
    <!-- Not Available * Container Service-->
    <!-- Not Available * Data Box-->
    <!-- Not Available * Dev Spaces-->
    <!-- Not Available * Dynamics LCS-->
* Express Route   <!-- Not Available * Lab Services-->
    <!-- Not Available * Managed Applications-->
    <!-- Not Available * Azure Genomics-->
* 安全性
* Site Recovery   <!-- Not Available * StorSimple Device Manager-->
* 虚拟网络（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)

## <a name="limitations"></a>限制

此部分说明如何处理移动资源时的复杂方案。 限制如下：

* [虚拟机限制](#virtual-machines-limitations)
* [虚拟网络限制](#virtual-networks-limitations)
* [应用服务限制](#app-service-limitations)
* [应用服务证书限制](#app-service-certificate-limitations)
* [经典部署限制](#classic-deployment-limitations)
* [恢复服务限制](#recovery-services-limitations)
* [HDInsight 限制](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>虚拟机限制

可以移动包含托管磁盘、托管映像和托管快照的虚拟机，以及移动所含虚拟机使用托管磁盘的可用性集。

<!--MOONCAKE: Not Available on  Managed Disks in Availability Zones can't be moved to a different subscription.-->

以下方案尚不受支持：

* 证书存储在 Key Vault 中的虚拟机可以移动到同一订阅中的新资源组，但无法跨订阅进行移动。
* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。

若要移动使用 Azure 备份配置的虚拟机，请使用以下解决方法：

* 找到虚拟机的位置。
* 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_chinanorth2_1
* 如果在 Azure 门户中，则查看“显示隐藏的类型”
* 如果在 PowerShell 中，则使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* 如果在 CLI 中，则使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
* 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
* 删除完成后，即可移动虚拟机。
    <!--MOONCAKE: CUSTOMIZED ON Not Available on  You can move the vault and virtual machine to the target subscription. After the move, you can continue backups with no loss in data.-->
    <!--MOONCAKE: Not Available on * For information about moving Recovery Service vaults for backup, see [Recovery Services limitations](#recovery-services-limitations)-->

### <a name="virtual-networks-limitations"></a>虚拟网络限制

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要移动带网络接口卡的虚拟机，必须移动所有依赖的资源。 必须移动与该网络接口卡对应的虚拟网络、该虚拟网络的所有其他网络接口卡，以及 VPN 网关。

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

### <a name="app-service-limitations"></a>应用服务限制

移动应用服务资源的限制各不相同，具体取决于是在订阅内移动资源，还是将应用服务资源移到新的订阅。 如果 Web 应用使用应用服务证书，请参阅[应用服务证书限制](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>在同一订阅中移动

_在同一订阅中_移动 Web 应用时，无法移动第三方 SSL 证书。 不过，可以将 Web 应用移动到新的资源组而不移动其第三方证书，并且，应用的 SSL 功能仍然可以工作。

如果希望随 Web 应用移动 SSL 证书，请执行以下步骤：

1. 从 Web 应用中删除第三方证书，但保留证书的副本
2. 移动 Web 应用。
3. 将第三方证书上传到移动后的 Web 应用。

#### <a name="moving-across-subscriptions"></a>跨订阅移动

_在订阅之间_移动 Web 应用时存在以下限制：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果某个应用服务资源不再位于其原始资源组中，则必须首先将其移动回该原始资源组，然后才能将其在订阅之间移动。

<!--Pending for verify after tranlation-->

如果忘记了原始资源组，可以通过诊断来查找。 对于 Web 应用，请选择“诊断和解决问题”  。 然后，选择“配置和管理”。 

![选择诊断](./media/resource-group-move-resources/select-diagnostics.png)

选择“迁移选项”  。

![选择迁移选项](./media/resource-group-move-resources/select-migration.png)

选择通过建议的步骤来移动 Web 应用的选项。

![选择建议的步骤](./media/resource-group-move-resources/recommended-steps.png)

可以看到在移动资源之前需采取的建议操作。 该信息包含 Web 应用的原始资源组。

![建议](./media/resource-group-move-resources/recommendations.png)

<!--Pending for verify after tranlation-->

### <a name="app-service-certificate-limitations"></a>应用服务证书限制

可将应用服务证书移动到新的资源组或订阅。 如果应用服务证书已绑定到某个 Web 应用，必须先执行一些步骤，然后才能将资源移到新的订阅中。 移动资源之前，请在 Web 应用中删除 SSL 绑定和专用证书。 应用服务证书不需删除，只需删除 Web 应用中的专用证书。

### <a name="classic-deployment-limitations"></a>经典部署限制

移动通过经典模型部署的资源时，其选项各不相同，具体取决于是在订阅内移动资源，还是将资源移到新的订阅。

#### <a name="same-subscription"></a>同一订阅

在同一订阅内将资源从一个资源组移动到另一个资源组时，存在以下限制：

* 不能移动虚拟网络（经典）。
* 虚拟机（经典）必须与云服务一起移动。
* 移动云服务时，必须移动其所有虚拟机。
* 一次只能移动一项云服务。
* 一次只能移动一个存储帐户（经典）。
* 存储帐户（经典）与虚拟机或云服务不能在同一操作中移动。

要将经典资源移到同一订阅内的新资源组，请通过[门户](#use-portal)、Azure PowerShell、Azure CLI 或 REST API 使用标准移动操作。 使用的操作应与移动 Resource Manager 资源时所用的操作相同。

#### <a name="new-subscription"></a>新订阅

将资源移动到新订阅时，存在以下限制：

* 必须在同一操作中移动订阅中的所有经典资源。
* 目标订阅不得包含任何其他经典资源。
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的资源管理器移动命令。

若要将经典资源移动到新订阅，请使用特定于经典资源的 REST 操作。 若要使用 REST，请执行以下步骤：

1. 检查源订阅是否可以参与跨订阅移动。 使用以下操作：

    ```HTTP
    POST https://management.chinacloudapi.cn/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    ```

    在请求正文中包括：

    ```json
    {
        "role": "source"
    }
    ```

    验证操作的响应格式如下：

    ```json
    {
        "status": "{status}",
        "reasons": [
        "reason1",
        "reason2"
        ]
    }
    ```

2. 检查目标订阅是否可以参与跨订阅移动。 使用以下操作：

    ```HTTP
    POST https://management.chinacloudapi.cn/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    ```

    在请求正文中包含以下内容：

    ```json
    {
        "role": "target"
    }
    ```

    响应的格式与源订阅验证的响应格式相同。
3. 如果两个订阅都通过了验证，可使用以下操作将所有经典资源从一个订阅移动到另一个订阅：

    ```HTTP
    POST https://management.chinacloudapi.cn/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
    ```

    在请求正文中包括：

    ```json
    {
        "target": "/subscriptions/{target-subscription-id}"
    }
    ```

此操作可能需要运行几分钟。

### <a name="#recovery-services-limitations"></a>恢复服务限制

<!--MOONCAKE: Not Available on To move a Recovery Services vault, you must enroll in the [limited public preview](/backup/backup-azure-recovery-services-vault-overview)-->

<!--URL direct to backup-azure-recovery-services-vault-overview-->

<!--MOONCAKE: Not Available on Currently, you can move one Recovery Services vault, per region, at a time. You can't move vaults that back up Azure Files, Azure File Sync, or SQL in IaaS virtual machines.-->

<!--MOONCAKE: Not Available on If a virtual machine doesn't move with the vault, the current virtual machine recovery points stay in the vault until they expire. Whether the virtual machine moved with the vault or not, you can restore the virtual machine from the backup history in the vault.-->

恢复服务保管库不支持跨订阅备份。 

<!--MOONCAKE: Not Available on If you move a vault with virtual machine backup data across subscriptions, you must move your virtual machines to the same subscription, and use the same target resource group to continue backups.-->

<!--MOONCAKE: Not Available on Backup policies defined for the vault are kept after the vault moves. Reporting and monitoring must be set up again for the vault after the move.-->

若要将虚拟机移到新的订阅而不移动恢复服务保管库，请执行以下操作：

 1. 暂时停止备份
 1. [删除还原点](#virtual-machines-limitations)。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
 1. 将虚拟机移到新的订阅
 1. 在该订阅的新保管库中对其重新进行保护

移动不支持用于使用 Azure Site Recovery 设置灾难恢复的“存储”、“网络”或“计算”资源。 例如，假设已设置将本地计算机复制到存储帐户 (Storage1)，并且想要受保护的计算机在故障转移到 Azure 之后显示为连接到虚拟网络 (Network1) 的虚拟机 (VM1)。 不能在同一订阅中的资源组之间或在订阅之间移动这些 Azure 资源 - Storage1、VM1 和 Network1。

### <a name="hdinsight-limitations"></a>HDInsight 限制

可以将 HDInsight 群集移到新订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、NIC 或负载均衡器）。 此外，无法将连接到群集的虚拟机的 NIC 移到新的资源组。

将 HDInsight 群集移至新订阅时，请先移动其他资源（例如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 源订阅和目标订阅必须处于活动状态。 如果在启用已禁用的帐户时遇到问题，请[创建 Azure 支持请求](https://support.azure.cn/zh-cn/support/support-azure/?l=zh-cn)。 选择“订阅管理”  作为问题类型。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../active-directory/develop/quickstart-create-new-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

   对于 Azure PowerShell，请使用：

   ```powershell
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   对于 Azure CLI，请使用：

   ```azurecli
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   <!--Not Available on If the tenant IDs for the source and destination subscriptions aren't the same, use the following methods to reconcile the tenant IDs:-->
   <!--Not Available on [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md)-->
   <!--Not Available on [How to associate or add an Azure subscription to Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)-->

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则，会收到错误，指明 **未针对资源类型注册订阅**。 将资源移到新的订阅时，可能会看到此错误，但该订阅从未配合该资源类型使用。

    对于 PowerShell，请使用以下命令来获取注册状态：

    ```powershell
    Set-AzContext -Subscription <destination-subscription-name-or-id>
    Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
    ```

    若要注册资源提供程序，请使用：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    ```

    对于 Azure CLI，请使用以下命令来获取注册状态：

    ```azurecli
    az account set -s <destination-subscription-name-or-id>
    az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
    ```

    若要注册资源提供程序，请使用：

    ```azurecli
    az provider register --namespace Microsoft.Batch
    ```

1. 移动资源的帐户至少需要具备下列权限：

    * 源资源组上的 Microsoft.Resources/subscriptions/resourceGroups/moveResources/action  权限。
    * 目标资源组上的 Microsoft.Resources/subscriptions/resourceGroups/write  权限。

1. 在移动资源之前，请检查要将资源移动到的订阅的订阅配额。 如果移动资源意味着订阅将超出其限制，则需要检查是否可以请求增加配额。 有关限制的列表及如何请求增加配额的信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。

1. 在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中有 800 多项资源时，资源管理器会立即返回错误。 但是，移动 800 项以下的资源也可能因超时而失败。

1. 服务必须支持移动资源的功能。 若要确定移动是否会成功，[验证你的移动请求](#validate-move)。 请参阅本文中的以下部分，了解[支持对资源进行移动的服务](#services-that-can-be-moved)和[不支持对资源进行移动的服务](#services-that-cannot-be-moved)。

## <a name="validate-move"></a>验证移动

[验证移动操作](https://docs.microsoft.com/rest/api/resources/resources/validatemoveresources)可以测试你的移动方案而无需实际移动资源。 使用此操作来确定移动是否会成功。 若要运行此操作，需要：

* 源资源组的名称
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID
* 你的帐户的[访问令牌](https://docs.microsoft.com/rest/api/azure/#acquire-an-access-token)

发送以下请求：

```
POST https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

包含请求正文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果请求格式正确，则操作将返回：

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.chinacloudapi.cn/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 状态代码指示已接受验证请求，但尚未确定移动操作是否会成功。 `location` 值包含用于检查长时间运行操作的状态的 URL。  

若要检查状态，请发送以下请求：

```
GET <location-url>
Authorization: Bearer <access-token>
```

操作仍在运行时，会继续收到 202 状态代码。 请等待 `retry-after` 值中所示的秒数，然后重试。 如果移动操作验证成功，则会收到 204 状态代码。 如果移动验证失败，则会收到错误消息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>移动资源

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />使用 Azure 门户

若要移动资源，请选择包含这些资源的资源组，然后选择“移动”  按钮。

![移动资源](./media/resource-group-move-resources/select-move.png)

选择是要将资源移到新资源组还是新订阅。

选择要移动的资源和目标资源组。 确认需要更新这些资源的脚本，选择“确定”  。 如果在上一步中已选择“编辑订阅”图标，则还必须选择目标订阅。

![选择目标](./media/resource-group-move-resources/select-destination.png)

在“通知”  中，可以看到移动操作正在运行。

![显示移动状态](./media/resource-group-move-resources/show-status.png)

操作完成后，你会获得结果通知。

![显示移动结果](./media/resource-group-move-resources/show-result.png)

<a name="use-powershell"></a>
### <a name="by-using-azure-powershell"></a>使用 Azure PowerShell

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```powershell
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

<a name="use-azure-cli"></a>
### <a name="by-using-azure-cli"></a>使用 Azure CLI

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](https://docs.azure.cn/zh-cn/cli/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

<a name="use-rest-api"></a>
### <a name="by-using-rest-api"></a>使用 REST API

要将现有资源移到另一个资源组或订阅中，请运行：

```HTTP
POST https://management.chinacloudapi.cn/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在请求正文中，指定目标资源组和要移动的资源。 有关移动 REST 操作的详细信息，请参阅 [移动资源](https://docs.microsoft.com/rest/api/resources/Resources/MoveResources)。

## <a name="next-steps"></a>后续步骤

* 若要了解管理资源所需的 PowerShell cmdlet，请参阅[将 Azure PowerShell 与资源管理器配合使用](manage-resources-powershell.md)。
* 若要了解管理资源所需的 Azure CLI 命令，请参阅[将 Azure CLI 与资源管理器配合使用](manage-resources-cli.md)。
* 若要了解管理订阅所需的门户功能，请参阅[使用 Azure 门户管理资源](resource-group-portal.md)。
* 若要了解如何向资源应用逻辑组织，请参阅[使用标记组织资源](resource-group-using-tags.md)。

<!--Update_Description: update meta properties, wording update, update link -->
