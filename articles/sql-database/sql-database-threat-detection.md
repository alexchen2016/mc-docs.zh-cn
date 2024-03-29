---
title: 威胁检测 - Azure SQL 数据库 | Microsoft Docs
description: 威胁检测会检测异常的数据库活动，这些活动指示单一数据库或弹性池中存在对数据库的潜在安全威胁。
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
origin.date: 02/08/2019
ms.date: 04/15/2019
ms.openlocfilehash: 99839d50427ff93e1dc08e341d75fbadd2c9cb36
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529419"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>针对单一数据库或入池数据库的 Azure SQL 数据库威胁检测

针对单一数据库或入池数据库的[威胁检测](sql-database-threat-detection-overview.md)可以检测异常活动，这些活动指示对数据库的异常和可能有害的访问或利用企图。 威胁检测可以识别**潜在的 SQL 注入**、**来自异常位置或数据中心的访问**、**来自陌生主体或可能有害的应用程序的访问**以及**暴力攻击 SQL 凭据** - 请在[威胁检测警报](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中查看更多详细信息。

可以通过 [Azure 门户](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收有关检测到的威胁的通知

[威胁检测](sql-database-threat-detection-overview.md)是[高级数据安全](sql-database-advanced-data-security.md) (ADS) 产品/服务（它是高级 SQL 安全功能的一个统一包）的一部分。 可通过中心 SQL ADS 门户访问和管理威胁检测。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库设置威胁检测

1. 在 [https://portal.azure.cn](https://portal.azure.cn) 中启动 Azure 门户。
2. 导航到要保护的 Azure SQL 数据库服务器的配置页。 在安全设置中，选择“高级数据安全”。
3. 在“高级数据安全”配置页：

   - 在服务器上启用高级数据安全。
   - 在“威胁检测设置”中的“发送警报到”文本框中，提供检测到异常数据库活动时收到安全警报的电子邮件列表。
  
   ![设置威胁检测](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 设置威胁检测

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解[威胁检测](sql-database-threat-detection-overview.md)。
- 详细了解[高级数据安全](sql-database-advanced-data-security.md)。
- 详细了解[审核](sql-database-auditing.md)
- 有关定价的详细信息，请参阅 [SQL 数据库定价页](https://azure.cn/pricing/details/sql-database/)  
