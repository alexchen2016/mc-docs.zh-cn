---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Python 开发函数
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, 函数, 事件处理, 动态计算, 无服务体系结构, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
origin.date: 04/16/2018
ms.date: 06/04/2019
ms.author: v-junlch
ms.openlocfilehash: b3681508b56d03742f0933950eea9dc7f630933f
ms.sourcegitcommit: 9e839c50ac69907e54ddc7ea13ae673d294da77a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491435"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>编程模型

Azure 函数应是 Python 脚本中处理输入并生成输出的无状态方法。 默认情况下，运行时期望此方法在 `__init__.py` 文件中作为名为 `main()` 的全局方法实现。

可以通过在 `function.json` 文件中指定 `scriptFile` 和 `entryPoint` 属性来更改默认配置。 例如，下面的 _function.json_ 指示运行时使用 _main.py_ 文件中的 _customentry()_ 方法作为 Azure 函数的入口点。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

来自触发器和绑定的数据使用在 `function.json` 配置文件中定义的 `name` 属性，通过方法特性绑定到函数。 例如，下面的 _function.json_ 描述一个由名为 `req` 的 HTTP 请求触发的简单函数：

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` 文件包含以下函数代码：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

（可选）还可以使用 Python 类型注释在函数中声明参数类型和返回类型。 例如，可以按如下所示，使用注释编写相同的函数：

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

使用 [ azure.functions.*](https://docs.microsoft.com/python/api/azure-functions/azure.functions?view=azure-python) 包中附带的 Python 注释将输入和输出绑定到方法。 

## <a name="folder-structure"></a>文件夹结构

Python 函数项目的文件夹结构如下所示：

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

有一个共享的 [host.json](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都有自己的代码文件和绑定配置文件 (function.json)。 

共享代码应保留在单独的文件夹中。 若要引用 SharedCode 文件夹中的模块，可以使用以下语法：

```
from ..SharedCode import myFirstHelperFunction
```

Functions 运行时使用的绑定扩展在 `extensions.csproj` 文件中定义，实际库文件位于 `bin` 文件夹中。 本地开发时，必须使用 Azure Functions Core Tools [注册绑定扩展](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles)。 

在 Azure 中将 Functions 项目部署到函数应用时，FunctionApp 文件夹的整个内容应包含在包中，但不包含该文件夹本身。

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和附加输入。 虽然它们在 `function.json` 中并不相同，但它们在 Python 代码中的使用方法却是相同的。  触发器和输入源的连接字符串应映射到本地 `local.settings.json` 文件中的值，以及在 Azure 中运行时的应用程序设置。 请看以下代码片段示例：

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

调用函数时，HTTP 请求作为 `req` 传递给函数。 将基于路由 URL 中的 _ID_ 从 Azure Blob 存储检索一个条目，并在函数体中将其用作 `obj`。  在这里，指定的存储帐户是在 `AzureWebJobsStorage` 中找到的连接字符串，它与函数应用使用的存储帐户相同。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出，请使用 `azure.functions.Out` 接口提供的 `set()` 方法将值分配给绑定。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="importing-shared-code-into-a-function-module"></a>将共享代码导入函数模块中

与函数模块一起发布的 Python 模块必须使用相关导入语法进行导入：

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

或者，将共享代码置于独立包中，将它发布到公共或专用 PyPI 实例，并将它指定为常规依赖项。

## <a name="async"></a>异步

由于对于每个函数应用只能存在单个 Python 进程，因此建议使用 `async def` 语句将 Azure 函数实现为异步协同程序。

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

如果 main() 函数是同步函数（无 `async` 限定符），则我们会在 `asyncio` 线程池中自动运行它。

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请在其签名中包含 `context` 参数。 

例如：

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Context** 类具有以下方法：

`function_directory`  
在其中运行函数的目录。

`function_name`  
函数的名称。

`invocation_id`  
当前函数调用的 ID。

## <a name="python-version-and-package-management"></a>Python 版本和包管理

当前，Azure Functions 仅支持 Python 3.6.x（CPython 正式分发版）。

在使用 Azure Functions Core Tools 或 Visual Studio Code 进行本地开发时，将所需包的名称和版本添加到 `requirements.txt` 文件并使用 `pip` 安装它们。

例如，可以使用以下要求文件和 pip 命令从 PyPI 安装 `requests` 包。

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

准备好进行发布时，确保所有依赖项都在 `requirements.txt` 文件（位于项目目录的根目录）中列出。 若要成功执行 Azure Functions，要求文件应至少包含以下包：

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>发布到 Azure

如果使用的包需要编译器并且不支持从 PyPI 安装 manylinux 兼容滚轮的包，则发布到 Azure 会失败并出现以下错误： 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

若要自动生成并配置所需二进制文件，请在本地计算机上[安装 Docker](https://docs.docker.com/install/)并运行以下命令以使用 [Azure Functions Core Tools](functions-run-local.md#v2) (func) 进行发布。 请记住将 `<app name>` 替换为 Azure 中的函数应用名称。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

实际上，Core Tools 会使用 docker 将 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作为本地计算机上的容器来运行。 它随后会使用此环境从源分发版生成并安装所需模块，然后再打包它们以便最终部署到 Azure。

> [!NOTE]
> Core Tools (func) 使用 PyInstaller 程序将用户的代码和依赖项冻结到单个独立可执行文件中以在 Azure 中运行。 此功能当前处于预览状态，可能不会扩展到所有类型的 Python 包。 如果无法导入模块，请使用 `--no-bundler` 选项尝试再次发布。 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> 如果继续遇到问题，请通过[建立问题](https://github.com/Azure/azure-functions-core-tools/issues/new)并包含问题描述来告知我们。 


## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

所有已知问题和功能请求都使用 [GitHub 问题](https://github.com/Azure/azure-functions-python-worker/issues)列表进行跟踪。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Blob 存储绑定](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)
* [存储绑定](functions-bindings-storage-queue.md)
* [计时器触发器](functions-bindings-timer.md)

<!-- Update_Description: wording update -->