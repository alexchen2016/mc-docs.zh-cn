---
title: 高级威胁防护 - Azure SQL 数据库 | Microsoft Docs
description: 高级威胁防护会检测异常的数据库活动，指出 Azure SQL 数据库中的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 03/31/2019
ms.date: 04/15/2019
ms.openlocfilehash: e7137632f7880ef613922000a975391af13a2e77
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529295"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 数据库的高级威胁防护

适用于 [Azure SQL 数据库](sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的高级威胁防护可检测异常活动，这些活动指示对数据库的异常和可能有害的访问或利用企图。

高级威胁防护包含在[高级数据安全性](sql-database-advanced-data-security.md) (ADS) 产品/服务中，是一个针对高级 SQL 安全功能的统一软件包。 可通过中心 SQL ADS 门户访问和管理高级威胁防护。

> [!NOTE]
> 本主题适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护

 高级威胁防护提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问和查询模式时，用户将收到警报。 高级威胁防护将警报与 Azure 安全中心集成，其中包含可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用高级威胁防护轻松解决数据库的潜在威胁。

为了提供完整的调查体验，建议启用 [SQL 数据库审核](sql-database-auditing.md)，它会将数据库事件写入到 Azure 存储帐户中的审核日志。  

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报

适用于 Azure SQL 数据库的高级威胁防护可检测异常活动（指示异常和可能有害的数据库访问或使用企图），并可触发以下警报：

- **存在易受 SQL 注入攻击的漏洞**：当某个应用程序在数据库中生成错误的 SQL 语句时，会触发此警报。 此警报会指示可能存在易受 SQL 注入攻击的漏洞。 生成错误语句的可能原因有两个：

  - 应用程序代码中的缺陷导致构造出错误的 SQL 语句
  - 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
- **潜在 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。
- **来自异常位置的访问**：当 SQL Server 的访问模式发生更改，有人从异常的地理位置登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自异常 Azure 数据中心的访问**：当 SQL Server 的访问模式发生更改，有人从最近在此服务器上出现过的 Azure 数据中心登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure SQL 查询编辑器中发布新应用程序）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **来自陌生主体的访问**：当 SQL Server 的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自可能有害的应用程序的访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 门户中浏览针对数据库发出的高级威胁防护警报

高级威胁防护功能将其警报与 Azure 安全中心集成。 Azure 门户中“数据库和 SQL ADS”边栏选项卡内的“实时 SQL 高级威胁防护”磁贴会跟踪活动威胁的状态。

单击“高级威胁防护警报”以启动“Azure 安全中心警报”页，并获取在数据库或数据仓库中检测到的活动 SQL 威胁的概述。

   ![高级威胁防护警报](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![高级威胁防护警报 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>后续步骤

- 详细了解[单一数据库和共用数据库中的高级威胁防护](sql-database-threat-detection.md)。
- 详细了解[高级数据安全性](sql-database-advanced-data-security.md)。
- 详细了解 [Azure SQL 数据库审核](sql-database-auditing.md)
- 有关定价的详细信息，请参阅 [SQL 数据库定价页](https://azure.cn/pricing/details/sql-database/)  
