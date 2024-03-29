---
title: 为 Azure Stack 开发模板 | Microsoft 文档
description: 了解 Azure Stack 模板的最佳做法
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: fbfd7ea25b1eda709f051a22ba766f8cb2b4de0e
ms.sourcegitcommit: 6101e77a8a4b8285ddedcb5a0a56cd3884165de9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218293"
---
# <a name="azure-resource-manager-template-considerations"></a>Azure 资源管理器模板注意事项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

开发应用程序时，请务必确保模板可在 Azure 和 Azure Stack 之间移植。 本文提供有关开发 Azure 资源管理器[模板](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)的注意事项，以便用户可以为应用程序创建原型以及在 Azure 中测试部署而无需访问 Azure Stack 环境。

## <a name="resource-provider-availability"></a>资源提供程序可用性

计划部署的模板只能使用已在 Azure Stack 中发布或以预览版方式发布的 Azure 服务。

## <a name="public-namespaces"></a>公共命名空间

由于 Azure Stack 托管在数据中心中，它的服务终结点命名空间与 Azure 公有云不同。 因此，如果尝试将 Azure 资源管理器模板部署到 Azure Stack，这些模板中的硬编码公共终结点会失败。 可以使用 `reference` 和 `concatenate` 函数动态构建服务终结点，以便在部署期间从资源提供程序检索值。 例如，不需要在模板中硬编码 *blob.core.chinacloudapi.cn*，检索 [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) 即可动态设置 *osDisk.URI* 终结点：

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API 版本控制

Azure 服务版本在 Azure 和 Azure Stack 之间可能有所不同。 每个资源都需要有 **apiVersion** 属性，用于定义所提供的功能。 为了确保 API 版本在 Azure Stack 中兼容，以下 API 版本对于每个资源提供程序有效：

| 资源提供程序 | apiVersion |
| --- | --- |
| 计算 |`'2015-06-15'` |
| 网络 |`'2015-06-15'`, `'2015-05-01-preview'` |
| 存储 |`'2016-01-01'`、`'2015-06-15'`、`'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| 应用服务 |`'2015-08-01'` |

## <a name="template-functions"></a>模板函数

Azure 资源管理器[函数](../../azure-resource-manager/resource-group-template-functions.md)提供生成动态模板所需的功能。 例如，可以对如下任务使用函数：

* 连接或修整字符串。
* 引用其他资源的值。
* 对资源进行迭代以部署多个实例。

以下函数在 Azure Stack 中不可用：

* 跳过
* Take

## <a name="resource-location"></a>资源位置

在部署过程中，Azure 资源管理器模板使用 `location` 属性来放置资源。 在 Azure 中，位置是指中国北部或中国东部之类的区域。 在 Azure Stack 中，位置有所不同，因为 Azure Stack 在数据中心内。 若要确保模板可在 Azure 和 Azure Stack 之间转移，在部署单个资源时应引用资源组位置。 可以使用 `[resourceGroup().Location]` 执行此操作，以确保所有资源均继承资源组位置。 以下代码是在部署存储帐户时使用此函数的示例：

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)

<!-- Update_Description: wording update -->