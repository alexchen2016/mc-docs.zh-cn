---
title: Microsoft Azure 备份服务器 v3 发行说明
description: 本文提供了有关 MABS v3 的已知问题和解决方法的信息。
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-lingwu
ms.openlocfilehash: 9115a2ff88a2c24e0c8f990fd81e98914c34b484
ms.sourcegitcommit: c43ca3018ef00245a94b9a7eb0901603f62de639
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986996"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure 备份服务器发行说明
本文提供了 Microsoft Azure 备份服务器 (MABS) V3 的已知的问题和解决方法。

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>群集化工作负荷的备份和恢复失败

**说明：** 将 MABS V2 升级到 MABS V3 之后，群集化数据源（例如数据库可用性组 (DAG)中的 Hyper-V 群集或 SQL 群集 (SQL Always On) 或 Exchange）的备份/还原失败。

**变通方法：** 若要防止出现此问题，请打开 SQL Server Management Studio (SSMS)) 并在 DPM DB 上运行以下 SQL 脚本：


    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>升级到 MABS V3 在俄罗斯语区域设置中失败

**说明：** 从 MABS V2 升级到 MABS V3 在俄罗斯语区域设置中失败并且出现错误代码 **4387**。

**变通方法：** 执行以下步骤以使用俄罗斯语安装包升级到 MABS V3：

1.  [备份](https://docs.microsoft.com/zh-cn/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure)你的 SQL 数据库并卸载 MABS V2（在卸载期间选择保留受保护的数据）。
2.  在升级过程中升级到 SQL 2017（企业版）并卸载报告功能。
3. [安装](https://docs.microsoft.com/zh-cn/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS)。
4.  [安装](https://docs.microsoft.com/zh-cn/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS)。
5.  如[使用 SQL 2017 时的 SSRS 配置](https://docs.microsoft.com/zh-cn/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)中所述使用参数配置报告功能。
6.  [安装](backup-azure-microsoft-azure-backup.md) MABS V3。
7. 使用 SSMS [还原](https://docs.microsoft.com/zh-cn/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL 并如[此处](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))所述运行 DPM-Sync 工具。
8.  使用以下命令更新 dbo.tbl_DLS_GlobalSetting 表中的“DataBaseVersion”属性：

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  启动 MSDPM 服务。


