---
title: 使用 SAS 令牌和 Azure CLI 部署 Azure 模板 | Azure
description: 使用 Azure Resource Manager 和 Azure CLI 从使用 SAS 令牌保护的模板将资源部署到 Azure。
services: azure-resource-manager
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 05/31/2017
ms.date: 07/03/2017
ms.author: v-yeche
ms.openlocfilehash: 946f2a198904746006f8fdf32e2e1d8c429b8426
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52663567"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>使用 SAS 令牌和 Azure CLI 部署专用 Resource Manager 模板

如果模板驻留在存储帐户中，可以限制对该模板的访问，并在部署过程中提供共享访问签名 (SAS) 令牌。 本主题介绍如何将 Azure PowerShell 与 Resource Manager 模板配合使用在部署过程中提供 SAS 令牌。 

## <a name="add-private-template-to-storage-account"></a>将专用模板添加到存储帐户

可以将模板添加到存储帐户，并在部署过程中使用 SAS 令牌链接到这些模板。

> [!IMPORTANT]
> 通过执行以下步骤，只有帐户所有者可以访问包含模板的 blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好方法，但不应直接在模板中包括密码等敏感数据。
> 
> 

以下示例设置专用存储帐户容器并上传模板：

```azurecli
az group create --name "ManageGroup" --location "China East"
az storage account create \
    --resource-group ManageGroup \
    --location "China East" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>在部署期间提供 SAS 令牌
要在存储帐户中部署专用模板，请生成 SAS 令牌，并将其包括在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

有关将 SAS 令牌与链接模板配合使用的示例，请参阅[将已链接的模版与 Azure Resource Manager 配合使用](resource-group-linked-templates.md)。

## <a name="next-steps"></a>后续步骤
* 有关部署模板的简介，请参阅[使用 Resource Manager 模板和 Azure PowerShell 部署资源](resource-group-template-deploy-cli.md)。
* 有关用于部署模板的完整示例脚本，请参阅[部署 Resource Manager 模板脚本](resource-manager-samples-cli-deploy.md)
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。