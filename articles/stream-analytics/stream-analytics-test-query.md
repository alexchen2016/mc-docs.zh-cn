---
title: 使用样本数据测试 Azure 流分析作业
description: 本文介绍如何使用 Azure 门户测试 Azure 流分析作业、示例输入以及上传样本数据。
services: stream-analytics
author: lingliw
ms.author: v-lingwu
ms.reviewer: jasonh
manager: digimobile
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/27/2018
ms.date: 11/26/2018
ms.openlocfilehash: bc035d0bdc3a14ab5476b4492e4aeb7e8b2474d8
ms.sourcegitcommit: 884c387780131bfa2aab0e54d177cb61ad7070a3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65609827"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>使用样本数据测试流分析查询

使用 Azure 流分析，可以在 Azure 门户中上传样本数据并测试查询，而不需要启动或停止作业。

## <a name="upload-sample-data-and-test-the-query"></a>上传样本数据并测试查询

1. 登录到 Azure 门户。 

2. 找到现有流分析作业并选择它。

3. 在“流分析作业”页上的“作业拓扑”标题下，选择“查询”以打开“查询编辑器”窗口。 

4. 若要使用样本输入数据来测试查询，请右键单击任意输入，  并选择“上传文件中的样本数据”。 此数据必须以 JSON、CSV 或 AVRO 进行序列化。 示例输入必须以 UTF-8 编码并且未压缩。 仅支持使用逗号 (,) 分隔符来测试门户上的 CSV 输入。

    ![流分析查询编辑器测试查询](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. 完成上传后，选择“测试”，以针对已提供的样本数据来测试此查询。

    ![流分析查询编辑器测试示例数据](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. 如果需要测试输出以供将来使用，浏览器中显示查询输出，以及用于下载结果的链接。 

7. 反复修改查询，并对其再次进行测试，以查看输出有何变化。

   ![流分析查询编辑器示例输出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   在查询中使用多个输出时，结果将显示在单独的选项卡上，可以在它们之间轻松切换。

8. 验证浏览器中显示的结果后，**保存**查询。 然后**启动**作业，并让它处理传入事件。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)

<!--Update_Description: update meta properties, wording update -->