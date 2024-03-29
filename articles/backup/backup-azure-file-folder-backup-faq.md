---
title: 使用 Azure 备份对文件和文件夹进行备份时的常见问题
description: 解决与使用 Azure 备份对文件和文件夹进行备份相关的常见问题。
author: lingliw
manager: digimobile
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: v-lingwu
ms.openlocfilehash: b0fd39f9553338254c2d80e214432b071674e2d3
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555447"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>与对文件和文件夹进行备份相关的常见问题 

本文解答了与使用 [Azure 备份](backup-overview.md)服务中的 Microsoft Azure 恢复服务 (MARS) 代理对文件和文件夹进行备份相关的常见问题。

## <a name="general"></a>常规

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>MARS 代理为什么需要 .NET framework 4.5.2 或更高版本？

[即时还原](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)中的新功能需要 .NET Framework 4.5.2 或更高版本。

## <a name="configure-backups"></a>配置备份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>可以从何处下载最新版本的 MARS 代理？ 
备份 Windows Server 计算机、System Center DPM 和 Microsoft Azure 备份服务器时使用的最新 MARS 代理可供[下载](https://aka.ms/azurebackup_agent)。 

### <a name="how-long-are-vault-credentials-valid"></a>保管库凭据的有效期是多长时间？
保管库凭据会在 48 小时后过期。 如果凭据文件过期，请重新从 Azure 门户下载该文件。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>可以从哪些驱动器备份文件和文件夹？ 

无法备份以下类型的驱动器/卷：

* 可移动媒体：所有备份项源必须报告为“固定”。
* 只读卷：为使卷影复制服务 (VSS) 起作用，卷必须是可写的。
* 脱机卷：为使 VSS 起作用，卷必须是联机的。
* 网络共享：卷必须位于需要使用联机备份进行备份的服务器本地。
* BitLocker 保护的卷：必须先解锁卷，才能备份。
* 文件系统标识：NTFS 是受支持的唯一文件系统。

### <a name="what-file-and-folder-types-are-supported"></a>支持哪些文件和文件夹类型？

支持以下类型：

* 加密
* 压缩
* 稀疏
* 压缩 + 稀疏
* 硬链接：不支持，跳过
* 重分析点：不支持，跳过
* 加密 + 稀疏：不支持，跳过
* 压缩流：不支持，跳过
* 重分析点，包括 DFS 链接和交接点


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 上备份文件和文件夹？  
是的。 Azure 备份使用 Azure VM 代理的 VM 扩展为 Azure VM 提供 VM 级备份。 如果你希望在 VM 的来宾 Windows 操作系统上备份文件和文件夹，可以安装 MARS 代理来执行该操作。 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 的临时存储上备份文件和文件夹？ 
是的。 安装 MARS 代理，将来宾 Windows 操作系统上的文件和文件夹备份到临时存储。 - 擦除临时存储数据后，备份作业会失败。
- 如果临时存储数据被删除，则你只能还原到非易失性存储。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何将服务器注册到其他区域？

备份数据会发送到服务器注册到的保管库的数据中心。 更改数据中心的最简便方法是卸载并重新安装代理，然后将计算机注册到所需区域中的新保管库

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理是否支持 Windows Server 2012 重复数据删除？
是的。 MARS 代理在准备备份操作时会将消除了重复的数据转换为常规数据。 然后，它将对数据进行优化以便备份、对数据进行加密，然后将已加密的数据发送到保管库。

## <a name="manage-backups"></a>管理备份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重命名了为备份配置的 Windows 计算机，将会发生什么情况？

重命名 Windows 计算机时，所有当前已配置的备份都将停止。

- 你需要向备份保管库注册新的计算机名称。
- 向保管库注册新名称时，第一个操作是“完整”备份。
- 如果需要恢复备份到采用旧服务器名称的保管库的数据，请使用“恢复数据”向导中用于还原到其他位置的选项。 [了解详细信息](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>用于备份的最大文件路径长度是多少？
MARS 代理依赖于 NTFS，并使用受 [Windows API](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths) 限制的文件路径长度规范。 如果你要保护的文件长于所允许的值，请备份父文件夹或磁盘驱动器。  

### <a name="what-characters-are-allowed-in-file-paths"></a>文件路径中允许什么字符？

MARS 代理依赖于 NTFS，并允许使用文件名/路径中[支持的字符](/windows/desktop/FileIO/naming-a-file#naming_conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出现“尚未为此服务器配置 Azure 备份”警告。
当本地服务器上存储的备份计划设置与备份保管库中存储的设置不同时，可能会出现此警告，即使你已配置了备份策略也是如此。
- 当服务器或设置恢复为已知良好状态后，备份计划可能会变得不同步。
- 如果收到此警告，请重新[配置](backup-azure-manage-windows-server.md)备份策略，并立即运行按需备份，以便将本地服务器与 Azure 重新同步。


## <a name="manage-the-backup-cache-folder"></a>管理备份缓存文件夹

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>针对缓存文件夹的最小大小要求是什么？
缓存文件夹的大小取决于要备份的数据量。
- 缓存文件夹卷的可用空间应当至少为备份数据总大小的 5-10%。
- 如果卷的可用空间少于 5%，请增大卷大小，或将缓存文件夹移到具有足够空间的卷。
- 
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何更改 MARS 代理的缓存位置？


1. 通过在权限提升的命令提示符下运行以下命令来停止备份引擎：

    ```PS C:\> Net stop obengine```

2. 不要移动文件。 请将缓存空间文件夹复制到具有足够空间的其他驱动器。
3. 更新以下注册表项，使路径指向新的缓存文件夹的路径。<br/>

    | 注册表路径 | 注册表项 | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

4. 在提升的命令提示符下重启备份引擎：

    ```PS C:\> Net start obengine```

5. 在使用新位置成功完成备份后，可以删除原始缓存文件夹。


### <a name="where-should-the-cache-folder-be-located"></a>缓存文件夹应当位于何处？

建议不要将缓存文件夹放在以下位置：

* 网络共享/可移动媒体：缓存文件夹必须位于需要使用联机备份进行备份的服务器本地。 不支持网络位置或可移动媒体，例如 U 盘
* 脱机卷：缓存文件夹必须联机才能使用 Azure 备份代理执行预期的备份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>缓存文件夹是否有任何不受支持的属性？
缓存文件夹不支持以下属性或其组合：

* 加密
* 已删除重复数据
* 压缩
* 稀疏
* 重分析点

缓存文件夹和元数据 VHD 没有 Azure 备份代理所需的属性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有办法调整用于备份的带宽量？
 
是的，可以使用 MARS 代理中的“更改属性”选项来调整带宽和定时。 [了解详细信息](backup-configure-vault.md#enable-network-throttling)**。

## <a name="restore"></a>还原

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，则还原过程会停止。 在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。


## <a name="next-steps"></a>后续步骤

[了解](tutorial-backup-windows-server-to-azure.md)如何备份 Windows 计算机。
