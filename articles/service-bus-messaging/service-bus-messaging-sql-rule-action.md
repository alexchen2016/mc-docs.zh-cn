---
title: Azure 中的 SQLRuleAction 语法参考
description: 有关 SQLRuleAction 语法的详细信息。
services: service-bus-messaging
documentationcenter: na
author: lingliw
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 09/05/2018
ms.date: 10/31/2018
ms.author: v-lingwu
ms.openlocfilehash: f6a7bc4259ee25b087c7e667d11df773d955202c
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52662854"
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction 语法

SqlRuleAction 是 [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) 类的实例，代表以基于 SQL 语言的语法编写的一组操作，该语法针对 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 执行。   
  
本文列出了有关 SQL 规则操作语法的详细信息。  

```  
<statements> ::=
    <statement> [, ...n]  

```  

```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  

```  

```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 

## <a name="arguments"></a>参数  
  
-   `<scope>` 是一个可选字符串，指示 `<property_name>` 的范围。 有效值为 `sys` or `user`进行求值的基于 SQL 语言的筛选器表达式。 `sys` 值指示系统范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的公共属性名称。 `user` 指示用户范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)字典的项。 `user` 范围是默认范围（如果 `<scope>` 未指定）。  
  
### <a name="remarks"></a>备注  

访问不存在的系统属性的尝试是错误，访问不存在的用户属性的尝试不是错误。 相反，不存在的用户属性在内部作为未知值进行求值。 在运算符求值过程中，未知值的处理方式很特殊。  

## <a name="propertyname"></a>property_name  

```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  

<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  

```  

### <a name="arguments"></a>参数  
 `<regular_identifier>` 是字符串，由以下正则表达式表示：  

```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  

 这是指以字母开头且后跟一个或多个下划线/字母/数字的任何字符串。  

 `[:IsLetter:]` 是指分类为 Unicode 字母的任何 Unicode 字符。 `System.Char.IsLetter(c)` 返回 `true`（如果 `c` 为 Unicode 字母）。  

 `[:IsDigit:]` 是指分类为十进制数字的任何 Unicode 字符。 `System.Char.IsDigit(c)` 返回 `true`（如果 `c` 为 Unicode 数字）。  

 `<regular_identifier>` 不能是保留关键字。  

 `<delimited_identifier>` 是用左/右方括号 ([]) 括起来的任何字符串。 右方括号采用两个右方括号的形式。 下面是 `<delimited_identifier>`的示例：  

```  
[Property With Space]  
[HR-EmployeeID]  

```  

 `<quoted_identifier>` 是指使用双引号引起来的任何字符串。 标识符中的双引号以两个双引号表示。 建议不要使用带引号的标识符，因为很容易与字符串常量混淆。 如果可能，请使用分隔标识符。 下面是 `<quoted_identifier>`的示例：  

```  
"Contoso & Northwind"  
```  

## <a name="pattern"></a>pattern  

```  
<pattern> ::=  
      <expression>  
```  

### <a name="remarks"></a>备注

 `<pattern>` 必须是作为字符串进行求值的表达式。 它用作 LIKE 运算符的模式。      它可以包含以下通配符：  

-   `%`：包含零个或多个字符的任意字符串。  

-   `_`：任何单个字符。  

## <a name="escapechar"></a>escape_char  

```  
<escape_char> ::=  
      <expression>  
```  

### <a name="remarks"></a>备注

 `<escape_char>` 必须是作为长度为 1 的字符串进行求值的表达式。 作为转义符用于 LIKE 运算符。  

 例如，`property LIKE 'ABC\%' ESCAPE '\'` 匹配 `ABC%`，而不匹配以 `ABC` 开头的字符串。  

## <a name="constant"></a>constant  

```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  

### <a name="arguments"></a>参数  

-   `<integer_constant>` 是指不使用引号引起来且不包含小数点的数字字符串。 这些值作为 `System.Int64` 在内部存储，并具有相同的作用域。  

     下面是长整数常量的示例：  

    ```  
    1894  
    2  
    ```  

-   `<decimal_constant>` 是指不使用引号引起来但包含小数点的数字字符串。 这些值作为 `System.Double` 在内部存储，并具有相同的作用域/精度。  

     在未来版本中，此数字可能以其他数据类型存储，目的是支持确切的数字语义，因此不应依赖于 `<decimal_constant>` 的基础数据类型为 `System.Double` 这一事实。  

     下面是十进制常量的示例：  

    ```  
    1894.1204  
    2.0  
    ```  

-   `<approximate_number_constant>` 是指使用科学记数法书写的数字。 这些值作为 `System.Double` 在内部存储，并具有相同的作用域/精度。 下面是近似数常量的示例：  

    ```  
    101.5E5  
    0.5E-2  
    ```  

## <a name="booleanconstant"></a>boolean_constant  

```  
<boolean_constant> :=  
      TRUE | FALSE  
```  

### <a name="remarks"></a>备注

布尔常量以关键字 `TRUE` 或 `FALSE` 表示。 这些值作为 `System.Boolean`存储。  

## <a name="stringconstant"></a>string_constant  

```  
<string_constant>  
```  

### <a name="remarks"></a>备注

字符串常量使用单引号引起来，并包含任何有效的 Unicode 字符。 字符串常量中嵌入的单引号以两个单引号表示。  

## <a name="function"></a>function  

```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  

### <a name="remarks"></a>备注  

`newid()` 函数返回 `System.Guid.NewGuid()` 方法生成的 **System.Guid**。  

`property(name)` 函数返回 `name` 所引用的属性的值。 `name` 值可以是返回字符串值的任何有效表达式。  

## <a name="considerations"></a>注意事项

- SET 用于创建新属性或更新现有属性的值。
- REMOVE 用于删除属性。
- 表达式类型和现有属性类型不同时，SET 会在可能的情况下执行隐式转换。
- 如果引用不存在的系统属性，操作会失败。
- 如果引用不存在的用户属性，操作不会失败。
- 不存在的用户属性在内部的求值为“未知”，其遵循的语义与 [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) 相同（在对运算符求值时）。

## <a name="next-steps"></a>后续步骤

- [SQLRuleAction 类](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter 类](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
