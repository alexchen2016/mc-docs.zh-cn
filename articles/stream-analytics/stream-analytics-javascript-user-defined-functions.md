---
title: '教程：Azure 流分析 JavaScript 用户定义的函数 | Azure '
description: 在本教程中，将使用 JavaScript 用户定义的函数执行高级查询机制
keywords: javascript, 用户定义的函数, udf
services: stream-analytics
author: lingliw
manager: digimobile
ms.assetid: ''
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
origin.date: 04/01/2018
ms.date: 11/06/18
ms.workload: data-services
ms.author: v-lingwu
ms.openlocfilehash: dfa3e2165f0ca6545f58f2bc1180ab5f2be79761
ms.sourcegitcommit: cca72cbb9e0536d9aaddba4b7ce2771679c08824
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58544753"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>教程：Azure 流分析 JavaScript 用户定义的函数

Azure 流分析支持以 JavaScript 编写的用户定义的函数。 利用 JavaScript 提供的丰富 **String**、**RegExp**、**Math**、**Array** 和 **Date** 方法，可以更轻松地创建包含流分析作业的复杂数据转换。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 定义 JavaScript 用户定义的函数
> * 将函数添加到门户
> * 定义运行函数的查询

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="javascript-user-defined-functions"></a>JavaScript 用户定义的函数
JavaScript 用户定义的函数支持仅用于计算的且不需要外部连接的无状态标量函数。 函数的返回值只能是标量（单个）值。 将某个 JavaScript 用户定义的函数添加到作业后，可在查询中的任意位置使用该函数，如同内置标量函数一样。

下面是 JavaScript 用户定义的函数可派上用场的一些情景：
* 使用 **Regexp_Replace()** 和 **Regexp_Extract()** 等正则表达式函数分析和处理字符串
* 解码和编码数据，例如，二进制到十六进制的转换
* 使用 JavaScript **Math** 函数执行数学计算
* 执行排序、联接、查找和填充等数组操作

使用流分析中的 JavaScript 用户定义的函数无法实现以下目的：
* 调用外部 REST 终结点，例如，执行反向 IP 查找，或者从外部源提取引用数据
* 针对输入/输出执行自定义事件格式序列化或反序列化
* 创建自定义聚合

尽管函数定义中并不禁止 **Date.GetDate()** 或 **Math.random()** 等函数，但应该避免使用这些函数。 这些函数在每次被调用时**不会**返回相同的结果，并且 Azure 流分析服务不会保留函数调用和返回结果的日记。 当你或流分析服务重新启动某个作业时，如果某个函数针对相同的事件返回不同的结果，则无法保证可重复性。

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>在 Azure 门户中添加 JavaScript 用户定义的函数
若要在现有的流分析作业中创建一个简单的 JavaScript 用户定义函数，请执行以下步骤：

> [!NOTE]
> 这些步骤适用于配置为在云中运行的流分析作业。 如果流分析作业配置为在 Azure IoT Edge 上运行，请改用 Visual Studio 并[使用 C# 编写用户定义的函数](stream-analytics-edge-csharp-udf.md)。

1.  在 Azure 门户中找到流分析作业。

2. 在“作业拓扑”标题下，选择“函数”   。 此时显示一个空白的函数列表。

3.  若要新建用户定义函数，请选择“+添加”。 

4.  在“新建函数”边栏选项卡中，为“函数类型”选择“JavaScript”。    编辑器中会显示默认函数模板。

5.  为“UDF 别名”输入 **hex2Int**，并按如下所示更改函数实现： 

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6. 选择“其他安全性验证”  。 该函数随即显示在函数列表中。
7. 选择新的 **hex2Int** 函数并检查函数定义。 所有函数的函数别名带有 **UDF** 前缀。 在流分析查询中调用该函数时，需要 *包含该前缀* 。 在本例中，调用的是 **UDF.hex2Int**。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>在查询中调用 JavaScript 用户定义的函数

1. 在查询编辑器中的“作业拓扑”  标题下，选择“查询”  。
2.  编辑查询，并调用该用户定义的函数，如下所示：

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3. 若要上传示例数据文件，请右键单击作业输入。
4. 若要测试查询，请选择“测试”。 

## <a name="supported-javascript-objects"></a>支持的 JavaScript 对象
Azure 流分析 JavaScript 用户定义的函数支持标准的内置 JavaScript 对象。 有关这些对象的列表，请参阅 [Global Objects](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)（全局对象）。

### <a name="stream-analytics-and-javascript-type-conversion"></a>流分析和 JavaScript 类型转换

流分析查询语言与 JavaScript 支持的类型有差别。 下表列出了两者之间的转换映射：

流分析 | Javascript
--- | ---
bigint | Number（JavaScript 只能精确呈现最大 2^53 的整数）
DateTime | Date（JavaScript 仅支持毫秒）
Double | Number
nvarchar(MAX) | String
Record | Object
Array | Array
Null | Null

下面是 JavaScript 到流分析的转换：

Javascript | 流分析
--- | ---
Number | 如果数字已舍入并介于 long.MinValue 和 long.MaxValue 之间，则为 Bigint；否则为 double日期
Date | DateTime
String | nvarchar(MAX)
Object | Record
Array | Array
Null、Undefined | Null
其他任何类型（例如函数或错误） | 不支持（导致运行时错误）

## <a name="troubleshooting"></a>故障排除
JavaScript 运行时错误被视为严重错误，可通过活动日志查看。 如果要检索日志，请在 Azure 门户中转到用户的作业，并选择“活动日志”。 

## <a name="other-javascript-user-defined-function-patterns"></a>JavaScript 用户定义的函数的其他模式

### <a name="write-nested-json-to-output"></a>编写要输出的嵌套 JSON
如果后续处理步骤需要使用流分析作业输出作为输入并且要求采用 JSON 格式，可以编写要输出的 JSON 字符串。 以下示例调用 **JSON.stringify()** 函数封装输入的所有名称/值对，并将其写入为输出中的单个字符串值。

**JavaScript 用户定义的函数定义：**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**示例查询：**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止该作业，以后在需要时再重启该作业。 如果不打算继续使用该作业，请按照以下步骤删除本快速入门创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。   
2. 在资源组页上单击“删除”  ，在文本框中键入要删除的资源的名称，并单击“删除”  。

## <a name="get-help"></a>获取帮助
如需更多帮助，请访问我们的 [Azure 流分析论坛](https://www.azure.cn/support/contact/)。

<!-- Not Available on ## Next steps-->
<!-- Not Available on [Real-time Twitter sentiment analysis in Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)-->

<!--Update_Description: update meta properties, wording update -->