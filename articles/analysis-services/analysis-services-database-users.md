---
title: 管理 Azure Analysis Services 中的数据库角色和用户| Azure
description: 了解如何在 Azure 中管理 Analysis Services 服务器上的数据库角色和用户。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 04/15/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: ab80269916a2c25a58f6586865eb9a89790408d3
ms.sourcegitcommit: 9f7a4bec190376815fa21167d90820b423da87e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59529457"
---
# <a name="manage-database-roles-and-users"></a>管理数据库角色和用户

在模型数据库级别，所有用户都必须属于一个角色。 角色可针对模型数据库定义具有特定权限的用户。 添加到角色的任何用户或安全组都必须在与服务器相同的订阅的 Azure AD 租户中具有一个帐户。 

定义角色的方式根据使用的工具有所差异，但效果却是相同的。

角色权限包括：
*  **管理员** - 用户对数据库具有完全的权限。 具有管理员权限的数据库角色不同于服务器管理员。
*  **处理** - 用户可以连接到数据库并对其执行处理操作，分析模型数据库数据。
*  **读取** - 用户可以使用客户端应用程序连接到模型数据库数据并进行分析。

如果创建表格模型项目，请使用 SSDT 中的角色管理器创建角色并向这些角色添加用户或组。 如果向服务器部署，请使用 SSMS、[Analysis Services PowerShell cmdlet](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) 或[表格模型脚本语言](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) 添加或删除角色和用户成员。

> [!NOTE]
> 安全组必须已将 `MailEnabled` 属性设为 `True`。

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>在 SSDT 中添加或管理角色和用户  

1.  在 SSDT >“表格模型资源管理器”中，右键单击“角色”。  

2.  在“角色管理器”中单击“新建”。  

3.  键入角色名称。  

     默认情况下，对于每个新的角色，默认角色名称以递增方式进行编号。 建议键入可明确标识成员类型的名称，例如，财务经理或人力资源专员。  

4.  选择以下权限之一：  

    |权限|说明|  
    |----------------|-----------------|  
    |**无**|成员无法修改模型架构，也无法查询数据。|  
    |**读取**|成员可以（基于行筛选器）查询数据，但无法修改模型架构。|  
    |**读取和处理**|成员可以（基于行级筛选器）查询数据并运行“处理”和“全部处理”操作，但无法修改模型架构。|  
    |**处理**|成员可以运行“处理”和“全部处理”操作。 无法修改模型架构，也无法查询数据。|  
    |**管理员**|成员可以修改模型架构并查询所有数据。|   

5.  如果正在创建的角色具有“读取”或“读取和处理”权限，可以使用 DAX 公式添加行筛选器。 单击“行筛选器”选项卡，选择表，单击“DAX 筛选器”字段，并键入一个 DAX 公式。

6.  单击“成员” > “添加外部成员”  

8.  在“添加外部成员”中，按电子邮件地址输入租户 Azure AD 中的用户或组。 单击“确定”并关闭角色管理器后，角色和角色成员将显示在表格模型资源管理器中。 

     ![表格模型资源管理器中的角色和用户](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. 部署到 Azure Analysis Services 服务器。

## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>在 SSMS 中添加或管理角色和用户

若要向部署模型数据库添加角色和用户，必须以服务器管理员身份连接到服务器，或已经是具有管理员权限的数据库角色的成员。

1. 在对象资源管理器中，右击“角色” > “新建角色”。

2. 在“创建角色”中，输入角色名称和说明。

3. 选择权限。

   |            权限            |                                                说明                                                |
   |----------------------------------|-----------------------------------------------------------------------------------------------------------|
   | **完全控制（管理员）** |                   成员可以修改模型架构，处理并查询所有数据。                   |
   |       **处理数据库**       | 成员可以运行“处理”和“全部处理”操作。 无法修改模型架构，也无法查询数据。 |
   |             **读取**             |             成员可以（基于行筛选器）查询数据，但无法修改模型架构。             |


4. 单击“成员资格”，并按电子邮件地址在租户 Azure AD 中输入用户或组。

     ![添加用户](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. 如果正在创建的角色具有“读取”权限，可以使用 DAX 公式添加行筛选器。 单击“行筛选器”，选择表，并在“DAX 筛选器”字段中键入 DAX 公式。 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>使用 TMSL 脚本添加角色和用户

可在 SSMS 中的 XMLA 窗口中运行 TMSL 脚本或使用 PowerShell。 使用 [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) 命令和 [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) 对象。

**示例 TMSL 脚本**

在此示例中，B2B 外部用户和组添加到了具有 SalesBI 数据库读取权限的 Analyst 角色中。 外部用户和组必须均位于相同的租户 Azure AD 中。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>使用 PowerShell 添加角色和用户

[SqlServer](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) 模块提供任务特定的数据库管理 cmdlet，以及接受表格模型脚本语言 (TMSL) 查询或脚本的通用 Invoke-ASCmd cmdlet。 以下 cmdlet 用于管理数据库角色和用户。

|Cmdlet|说明|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference)|向数据库角色添加成员。| 
|[Remove-RoleMember](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference)|从数据库角色中删除成员。|   
|[Invoke-ASCmd](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference)|执行 TMSL 脚本。|

## <a name="row-filters"></a>行筛选器  

行筛选器定义特定角色的成员可以查询表中的哪些行。 可使用 DAX 公式为模型中的每个表定义行筛选器。  

可仅为具有“读取”和“读取和处理”权限的角色定义行筛选器。 默认情况下，如果没有为特定表定义行筛选器，除非交叉筛选其他表中的适用项，否则成员可以查询表中的所有行。

行筛选器需要 DAX 公式，该公式的求值结果必须为 TRUE/FALSE，以定义该特定角色的成员可以查询的行。 无法查询未包含在 DAX 公式中的行。 例如，具有以下行筛选器表达式的 Customers 表：*=Customers [Country] = "CHINA"*，Sales 角色的成员只能查看中国境内的客户。  

<!--MOONCAKE: Should Be China-->

行筛选器适用于指定的行和相关行。 如果表具有多个关系，筛选器将对处于活动状态的关系应用安全性。 行筛选器与为相关表定义的其他行筛选器相交，示例如下：  

|表|DAX 表达式|  
|-----------|--------------------|  
|区域|=Region[Country]="CHINA"|  
|ProductCategory|=ProductCategory[Name]="Bicycles"|  
|事务|=Transactions[Year]=2016|  

 净效果是成员可以查询若干行数据，其中客户位于中国，产品类别为自行车，年份是 2016 年。 用户无法查询中国之外的事务、不是自行车的事务或非 2016 年的事务，除非他们属于授予这些权限的另一角色。

<!--MOONCAKE: Should Be China-->

 可以使用筛选器 =FALSE() 拒绝访问整个表的所有行。

## <a name="next-steps"></a>后续步骤

  [管理服务器管理员](analysis-services-server-admins.md)   
  [使用 PowerShell 管理 Azure Analysis Services](analysis-services-powershell.md)  
  [表格模型脚本语言 (TMSL) 参考](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

<!--Update_Description: update meta properties, wording update -->