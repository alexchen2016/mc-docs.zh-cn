---
title: 使用模板验证工具检查 Azure Stack 的模板 | Microsoft Docs
description: 检查要部署到 Azure Stack 的模板
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/08/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 2839b00e3117396ce00f8530588c78c61d6bd642
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64855470"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>使用模板验证工具检查 Azure Stack 的模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用模板验证工具检查 Azure 资源管理器[模板](azure-stack-arm-templates.md)是否已准备好部署到 Azure Stack。 模板验证工具是 Azure Stack 工具的一部分。 使用[从 GitHub 下载工具](../operator/azure-stack-powershell-download.md)一文中所述的步骤下载 Azure Stack 工具。

## <a name="overview"></a>概述

若要验证模板，必须先生成云功能文件，然后运行验证工具。 使用 Azure Stack 工具中的以下 PowerShell 模块：

- 在 **CloudCapabilities** 文件夹中：`AzureRM.CloudCapabilities.psm1` 创建一个云功能 JSON 文件，该文件表示 Azure Stack 云中的服务和版本。
- 在 **TemplateValidator** 文件夹中：`AzureRM.TemplateValidator.psm1` 使用一个云功能 JSON 文件来测试要在 Azure Stack 中部署的模板。

## <a name="build-the-cloud-capabilities-file"></a>生成云功能文件

使用模板验证程序之前，先运行 **AzureRM.CloudCapabilities** PowerShell 模块以生成 JSON 文件。

>[!NOTE]
> 如果更新集成系统，或添加任何新服务或虚拟扩展，应重新运行该模块。

1. 请确保已连接到 Azure Stack。 这些步骤可从 Azure Stack 开发工具包主机执行，也可以使用 [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) 从工作站连接。
2. 导入 **AzureRM.CloudCapabilities** PowerShell 模块：

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. 使用 `Get-CloudCapabilities` cmdlet 检索服务版本，并创建云功能 JSON 文件。 如果未指定 **-OutputPath**，则将在当前目录中创建文件 AzureCloudCapabilities.Json。 使用你的实际 Azure 位置：

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>验证模板

按照这些步骤，使用 **AzureRM.TemplateValidator** PowerShell 模块验证模板。 可以使用自己的模板，或验证 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。

1. 导入 **AzureRM.TemplateValidator.psm1** PowerShell 模块：

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. 运行模板验证程序：

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

模板验证警告或错误会显示在 PowerShell 控制台中并写入到源目录中的一个 HTML 文件中。 以下屏幕截图是验证报告的一个示例：

![模板验证报告](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>参数

模板验证程序支持以下参数。

| 参数 | 说明 | 必需 |
| ----- | -----| ----- |
| TemplatePath | 指定要在其中递归查找 Azure 资源管理器模板的路径。 | 是 |
| TemplatePattern | 指定要匹配的模板文件的名称。 | 否 |
| CapabilitiesPath | 指定云功能 JSON 文件的路径。 | 是 |
| IncludeComputeCapabilities | 包括 IaaS 资源（例如 VM 大小和 VM 扩展）的评估。 | 否 |
| IncludeStorageCapabilities | 包括存储资源（例如 SKU 类型）的评估。 | 否 |
| 报表 | 指定生成的 HTML 报告的名称。 | 否 |
| 详细 | 将错误和警告记录到控制台。 | 否|

### <a name="examples"></a>示例

此示例验证下载到本地存储的所有 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。 此示例还根据 Azure Stack 开发工具包功能验证虚拟机大小和扩展：

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>后续步骤

- [将模板部署到 Azure Stack](azure-stack-arm-templates.md)
- [为 Azure Stack 开发模板](azure-stack-develop-templates.md)

<!-- Update_Description: wording update -->