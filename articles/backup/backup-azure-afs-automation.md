---
title: 使用 PowerShell 部署和管理 Azure 文件共享的备份
description: 使用 PowerShell 在 Azure 中部署和管理 Azure 文件共享的备份
services: backup
author: lingliw
manager: digimobile
keywords: PowerShell; Azure 文件备份; Azure 文件还原
ms.service: backup
ms.topic: conceptual
ms.date: 01/21/19
ms.author: v-lingwu
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: f0bcb7131bc45952c55b73847a954fd5b25b7b5c
ms.sourcegitcommit: 26957f1f0cd708f4c9e6f18890861c44eb3f8adf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363362"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>使用 PowerShell 备份和还原 Azure 文件共享

本文介绍如何使用 Azure PowerShell cmdlet 从恢复服务保管库备份和恢复 Azure 文件共享。 恢复服务保管库是一种 Azure 资源管理器资源，用于保护 Azure 备份和 Azure Site Recovery 服务中的数据与资产。

## <a name="concepts"></a>概念

如果你不熟悉 Azure 备份服务，请参阅[什么是 Azure 备份？](backup-introduction-to-azure-backup.md)以大致了解该服务。

要有效地使用 PowerShell，必须了解对象的层次结构以及开始位置。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

若要查看 AzureRm.RecoveryServices.Backup PowerShell cmdlet 参考，请参阅 Azure 库中的 [Azure Backup - Recovery Services cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)（Azure 备份 - 恢复服务 cmdlet）。

## <a name="setup-and-registration"></a>设置和注册

> [!NOTE]
> 如[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0)中所述，对 AzureRM 模块中新功能的支持将于 2018 年 11 月结束。 现已公开发布对新 Azure PowerShell 模块与 Azure 文件共享的备份提供支持。

请按照下列步骤开始。

1. [下载最新版本的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0)。 所需的最低版本为 1.0.0。

2. 键入以下命令查找可用的 Azure 备份 PowerShell cmdlet。

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    这将显示 Azure 备份、Azure Site Recovery 和恢复服务保管库的别名和 cmdlet。 下图是你将看到的示例。 这不是 cmdlet 的完整列表。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. 使用“Connect-AzAccount”登录到 Azure 帐户。 此 cmdlet 打开一个网页，提示输入帐户凭据：

    * 或者，可以使用 -Credential 参数，将帐户凭据作为参数包含在 Connect-AzAccount cmdlet 中。
    * 如果是代表租户的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户。 例如，Connect-AzAccount -Tenant fabrikam.com。

4. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 使用以下命令验证提供程序是否已成功注册。
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    在命令输出中，RegistrationState 更改为“已注册”。 如果没有看到此更改，请再次运行 Register-AzResourceProvider cmdlet。

使用 PowerShell 可以自动化以下任务：

* 创建恢复服务保管库。
* 配置 Azure 文件共享的备份。
* 触发备份作业。
* 监视备份作业。
* 还原 Azure 文件共享。
* 从 Azure 文件共享还原单个 Azure 文件。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

请按照以下步骤创建恢复服务保管库。

1. 恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. 使用 **New-AzRecoveryServicesVault** cmdlet 创建恢复服务保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 请指定要使用的存储冗余类型。 可以使用[本地冗余存储](../storage/common/storage-redundancy-lrs.md)或[异地冗余存储](../storage/common/storage-redundancy-grs.md)。 以下示例显示，“testvault”的“-BackupStorageRedundancy”选项设置为“GeoRedundant”。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用“Get-AzRecoveryServicesVault”。

```powershell
Get-AzRecoveryServicesVault
```

输出类似于以下示例。 请注意，提供了关联的“资源组名称”和“位置”。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。

使用 **Set-AzRecoveryServicesVaultContext** 设置保管库上下文。 设置保管库上下文后，它将应用于所有后续 cmdlet。 以下示例为 testvault 设置保管库上下文。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> 我们计划根据 Azure PowerShell 指南弃用保管库上下文的设置。 相反，建议用户传递以下说明中所述的保管库 ID。

或者，可以存储或提取要对其执行 PowerShell 操作的保管库的 ID，并将此 ID 传递给相关命令。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>配置 Azure 文件共享的备份

### <a name="create-a-protection-policy"></a>创建保护策略

一个备份保护策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。 使用 **Get-AzRecoveryServicesBackupRetentionPolicyObject** 查看默认保留策略。 

类似地，可以使用 Get-AzRecoveryServicesBackupSchedulePolicyObject 获取默认计划策略。 **New-AzRecoveryServicesBackupProtectionPolicy** cmdlet 创建用于保存备份策略信息的 PowerShell 对象。 计划和保留策略对象将用作 **New-AzRecoveryServicesBackupProtectionPolicy** cmdlet 的输入。 

以下示例将计划策略和保留策略存储在变量中。 该示例使用这些变量在创建 NewPolicy 保护策略时定义参数。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出类似于以下示例。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

“NewAFSPolicy”进行每日备份，并将其保留 30 天。

### <a name="enable-protection"></a>启用保护

定义保护策略后，可以使用此策略为 Azure 文件共享启用保护。

首先，使用 Get-AzRecoveryServicesBackupProtectionPolicy cmdlet 提取相关的策略对象。 请使用此 cmdlet 获取特定策略，或者查看与某个工作负载类型关联的策略。

以下示例获取适用于工作负载类型 AzureFiles 的策略。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

输出类似于以下示例。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中“BackupTime”字段的时区是通用协调时间 (UTC)。 在 Azure 门户中显示备份时间时，该时间会根据本地时区进行调整。
>
>

以下策略检索名为“dailyafs”的备份策略。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

使用 **Enable-AzRecoveryServicesBackupProtection** 通过给定的策略启用项保护。 将策略与保管库关联之后，会在策略计划中定义的时间触发备份工作流。

以下示例使用策略“dailyafs”，为存储帐户“testStorageAcct”下的 Azure 文件共享“testAzureFileShare”启用保护。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令将等到保护作业配置完成为止，然后提供如下所示的输出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>触发按需备份

使用 **Backup-AzRecoveryServicesBackupItem** 针对受保护的 Azure 文件共享触发备份作业。 使用以下命令检索存储帐户及其包含的文件共享，并触发按需备份。

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

该命令返回一个要跟踪的、带有 ID 的作业，如以下示例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

创建备份时使用 Azure 文件共享快照，因此通常作业在命令返回此输出时完成。

### <a name="modify-the-protection-policy"></a>修改保护策略

要更改用于保护 Azure 文件共享的策略，请结合相关的备份项和新的保护策略使用 Enable-AzRecoveryServicesBackupProtection。

以下示例将“testAzureFS”保护策略从“dailyafs”更改为“monthlyafs”。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>还原 Azure 文件共享和 Azure 文件

可将整个文件共享还原到其原始位置或备用位置。 同样，还可以还原文件共享中的单个文件。

### <a name="fetch-recovery-points"></a>提取恢复点

使用 **Get-AzRecoveryServicesBackupRecoveryPoint** cmdlet 列出备份项的所有恢复点。 在以下脚本中，变量“$rp”是一个数组，其中包含所选备份项在过去七天的恢复点。 该数组按时间进行反向排序，以最新的恢复点作为索引 0。 使用标准 PowerShell 数组索引选取恢复点。 在示例中，$rp[0] 选择最近的恢复点。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

输出类似于以下示例。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.chinacloudapi.cn/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

选择相关的恢复点之后，将文件共享或文件还原到备用或原始位置，如此处所述。

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>将 Azure 文件共享还原到备用位置

#### <a name="restore-an-azure-file-share"></a>还原 Azure 文件共享

提供以下信息来标识备用位置：

* **TargetStorageAccountName**：要将备份内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：目标存储帐户中要将备份内容还原到的文件共享。
* **TargetFolder**：文件共享中要将数据还原到的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **ResolveConflict**：与还原的数据发生冲突时提供的说明。 接受“覆盖”或“跳过”。

在 restore 命令中提供这些参数可将备份的文件共享还原到备用位置。

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

该命令返回一个要跟踪的、带有 ID 的作业，如以下示例所示。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restore-an-azure-file"></a>还原 Azure 文件

要还原单个文件而不是整个文件共享，请通过提供以下参数来唯一标识单个文件：

* **TargetStorageAccountName**：要将备份内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：目标存储帐户中要将备份内容还原到的文件共享。
* **TargetFolder**：文件共享中要将数据还原到的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **SourceFilePath**：文件共享中要还原的文件的绝对路径，字符串格式。 此路径与 Get-AzStorageFile PowerShell cmdlet 中使用的路径相同。
* **SourceFileType**：选择的是目录还是文件。 接受“目录”或“文件”。
* **ResolveConflict**：与还原的数据发生冲突时提供的说明。 接受“覆盖”或“跳过”。

附加参数仅与要还原的单个文件相关。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

此命令还返回一个要跟踪的、带有 ID 的作业，如前所示。

### <a name="restore-azure-file-shares-to-the-original-location"></a>将 Azure 文件共享还原到原始位置

在还原到原始位置时，无需指定所有与 destination- 和目标相关的参数。 仅“ResolveConflict”必须提供。

#### <a name="overwrite-an-azure-file-share"></a>覆盖 Azure 文件共享

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>覆盖 Azure 文件

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>跟踪备份和还原作业

按需备份和还原操作会返回一个要跟踪的、带有 ID 的作业，如上一节[“触发按需备份”](#trigger-an-on-demand-backup)中所示。 使用 Get AzRecoveryServicesBackupJobDetails cmdlet 来跟踪作业的进度和提取更多详细信息。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 21Vianet Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Azure support.
```
