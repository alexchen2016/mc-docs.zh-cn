---
title: 在 Azure 中使用 Visual Studio 创建你的第一个函数
description: 使用 Visual Studio 创建并发布一个 HTTP 触发的 Azure 函数。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
origin.date: 10/17/2018
ms.date: 06/03/2019
ms.author: v-junlch
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 71c3c07d8378cbc7f5bc5f44406e3ad551312171
ms.sourcegitcommit: 9e839c50ac69907e54ddc7ea13ae673d294da77a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491411"
---
# <a name="create-your-first-function-using-visual-studio"></a>使用 Visual Studio 创建你的第一个函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。

在本文中，你将了解如何使用 Azure Functions 的 Visual Studio 2019 工具在本地创建并测试“hello world”函数。 然后将函数代码发布到 Azure。 Visual Studio 2019 中的 Azure 开发工作负荷已随附这些工具。

![浏览器中的函数 localhost 响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)


## <a name="prerequisites"></a>先决条件

完成本教程：

* 安装 [Visual Studio 2019](/downloads/)，并确保还安装了 **Azure 开发**工作负荷。

* 请确保你有[最新的 Azure Functions 工具](functions-develop-vs.md#check-your-tools-version)。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="create-a-function-app-project"></a>创建函数应用项目

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio 将创建一个项目，并在该项目中创建一个包含所选函数类型的样本代码的类。 方法中的 **FunctionName** 属性设置函数的名称。 **HttpTrigger** 属性指定该函数将由某个 HTTP 请求触发。 样本代码发送 HTTP 响应，其中包含请求正文或查询字符串中的值。 可以通过向方法应用相应的属性，将输入和输出绑定添加到函数。 有关详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)的[触发器和绑定](functions-dotnet-class-library.md#triggers-and-bindings)部分。

创建函数项目和 HTTP 触发的函数后，可以在本地计算机上对其进行测试。

## <a name="test-the-function-locally"></a>在本地测试函数

使用 Azure Functions Core Tools 可以在本地开发计算机上运行 Azure Functions 项目。 首次从 Visual Studio 启动某个函数时，系统会提示你安装这些工具。

1. 若要测试函数，请按 F5。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具。 可能还需启用一个防火墙例外，以便这些工具能够处理 HTTP 请求。

2. 从 Azure Functions 运行时输出复制函数的 URL。

    ![Azure 本地运行时](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-debugging.png)

3. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并执行请求。 下面演示浏览器中函数返回的对本地 GET 请求的响应： 

    ![浏览器中的函数 localhost 响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local.png)

4. 若要停止调试，请按 **Shift + F5**。

验证该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 可以直接从 Visual Studio 创建函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中测试函数

1. 从“发布”配置文件页复制函数应用的基 URL。 将 URL 的 `localhost:port` 部分（在本地测试函数时使用）替换为新的基 URL。 与前面一样，请确保将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并执行请求。

    调用 HTTP 触发函数的 URL 应采用以下格式：

        http://<APP_NAME>.chinacloudsites.cn/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对远程 GET 请求的响应：

    ![浏览器中的函数响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>后续步骤

你已使用简单的 HTTP 触发函数通过 Visual Studio 创建和发布 C# 函数应用。

* [了解如何添加与其他服务集成的输入和输出绑定。](functions-develop-vs.md#add-bindings)
* [详细了解如何将函数开发为 .NET 类库](functions-dotnet-class-library.md)。

<!-- Update_Description: wording update -->