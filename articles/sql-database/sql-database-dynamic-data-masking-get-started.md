---
title: Azure SQL 数据库动态数据掩码 | Microsoft docs
description: SQL 数据库动态数据掩码通过对非特权用户模糊化敏感数据来控制此类数据的泄露
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto
manager: digimobile
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.openlocfilehash: e0d8b7f723fa2331aba32fc602d529ad9e7a3efa
ms.sourcegitcommit: 0777b062c70f5b4b613044804706af5a8f00ee5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003442"
---
# <a name="sql-database-dynamic-data-masking"></a>SQL 数据库动态数据掩码

SQL 数据库动态数据掩码通过对非特权用户模糊化敏感数据来限制此类数据的泄露。 

动态数据掩码允许客户指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。

例如，呼叫中心服务代表可以根据呼叫者的信用卡号的多个数字来识别其身份，但这些数据项不应完全透露给服务代表。 可以定义掩码规则，对任意查询的结果集中任何信用卡号除最后四位数以外的其他所有数字进行掩码。 另举一例，通过定义适当的数据掩码来保护个人身份信息 (PII) 数据，开发人员一方面可以查询生产环境以进行故障排除，同时又不违反法规遵从性要求。

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL 数据库动态数据掩码基础知识

通过在 SQL 数据库配置边栏选项卡或设置边栏选项卡中选择“动态数据掩码”操作，在 Azure 门户中设置动态数据掩码策略。

### <a name="dynamic-data-masking-permissions"></a>动态数据掩码权限

Azure SQL 数据库管理员、服务器管理员或 [SQL 安全管理员](/role-based-access-control/built-in-roles#sql-security-manager)角色可以配置动态数据掩码。

### <a name="dynamic-data-masking-policy"></a>动态数据掩码策略

* **不对其进行掩码的 SQL 用户** - 一组可以在 SQL 查询结果中获取非掩码数据的 SQL 用户或 AAD 标识。 始终不会对拥有管理员权限的用户进行掩码，这些用户可以看到没有任何掩码的原始数据。
* **掩码规则** - 一组规则，定义要掩码的指定字段，以及使用的掩码函数。 可以使用数据库架构名称、表名称和列名称定义指定的字段。
* **掩码函数** - 一组方法，用于控制不同情况下的数据透露。

| 掩码函数 | 掩码逻辑 |
| --- | --- |
| **默认** |**根据指定字段的数据类型完全掩码**<br/><br/>对于字符串数据类型（nchar、ntext、nvarchar），使用 XXXX；如果字段大小小于 4 个字符，则使用更少的 X。<br/>• 对于数字数据类型（bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real），使用零值。<br/>对于日期/时间数据类型（date、datetime2、datetime、datetimeoffset、smalldatetime、time），使用 1900-01-01。<br/>• 对于 SQL 变量，使用当前类型的默认值。<br/>• 对于 XML，使用文档 <masked/>。<br/>• 对于特殊数据类型（timestamp、table、hierarchyid、GUID、binary、image、varbinary 空间类型），将使用空值。 |
| **信用卡** |**此掩码方法公开指定字段的最后四位数**，并添加一个信用卡格式的常量字符串作为前缀。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**此掩码方法公开第一个字母并将域替换为 XXX.com**，并使用一个电子邮件地址格式的常量字符串作为前缀。<br/><br/>aXX@XXXX.com |
| **随机数** |**此掩码方法根据选定边界和实际数据类型生成随机数**。 如果指定的边界相等，则掩码函数是常数。<br/><br/>![导航窗格](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **自定义文本** |**此掩码方法公开第一个和最后一个字符**，并在中间添加一个自定义填充字符串。 如果原始字符串短于公开的前缀和后缀，则只使用填充字符串。 <br/>前缀 [填充] 后缀<br/><br/>![导航窗格](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>建议进行掩码的字段

DDM 建议引擎会将数据库中的某些字段标记为可能的敏感字段，可以考虑对这些字段进行掩码。 在门户的“动态数据掩码”边栏选项卡中，会看到针对数据库建议的列。 用户只需针对一个或多个列单击“添加掩码”，单击“保存”，即可对这些字段应用掩码。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 为数据库设置动态数据掩码

请参阅 [Azure SQL 数据库 Cmdlet](https://docs.microsoft.com/powershell/module/az.sql)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>使用 REST API 为数据库设置动态数据掩码

请参阅[对 Azure SQL 数据库的操作](https://docs.microsoft.com/rest/api/sql/)。
