---
author: rockboyfor
ms.service: azure-resource-manager
ms.topic: include
origin.date: 04/19/2019
ms.date: 06/03/2019
ms.author: v-yeche
ms.openlocfilehash: 7011660630a6006428b19fe3771fe5d96cc490fa
ms.sourcegitcommit: d75eeed435fda6e7a2ec956d7c7a41aae079b37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66224491"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[资源组](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的资源数（按资源类型） |800 |因资源类型而有所不同 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |800 |
| 每个部署的资源数 |800 |800 |
| 管理锁数（按唯一的作用域） |20 个 |20 个 |
| 标记数（按资源或资源组） |15 |15 |
| 标记键长度 |512 |512 |
| 标记值长度 |256 |256 |

<sup>1</sup>如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 从部署历史记录中删除条目不会影响已部署的资源。 可以使用 Azure CLI 的 [az group deployment delete](https://docs.azure.cn/zh-cn/cli/group/deployment?view=azure-cli-latest#az-group-deployment-delete) 或 PowerShell 中的 [Remove-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroupdeployment) 删除历史记录中的条目。  有关在持续集成和持续交付 (CI/CD) 方案中用于自动删除部署的 PowerShell 脚本，请参阅 [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)。

#### <a name="template-limits"></a>模板限制

| 值 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 参数 |256 |256 |
| 变量 |256 |256 |
| 资源（包括副本计数） |800 |800 |
| 输出 |64 |64 |
| 模板表达式 |24,576 个字符 |24,576 个字符 |
| 已导出模板中的资源 |200 |200 | 
| 模板大小 |1 MB |1 MB |
| 参数文件大小 |64 KB |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 

<!-- Not Available on Microsoft Azure content  [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).-->
