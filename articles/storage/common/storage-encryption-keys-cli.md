---
title: 通过 Azure CLI 配置客户管理的密钥用于 Azure 存储加密
description: 了解如何使用 Azure CLI 来配置客户管理的密钥用于 Azure 存储加密。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/16/2019
ms.date: 05/27/2019
ms.author: v-jay
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1c3978ab77ac833b78b02df8dac7e165d599a0f1
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004358"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>通过 Azure CLI 配置客户管理的密钥用于 Azure 存储加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文介绍如何使用 Azure CLI 配置包含客户管理的密钥的 Key Vault。

## <a name="assign-an-identity-to-the-storage-account"></a>将标识分配到存储帐户

若要为存储帐户启用客户管理的密钥，请先将一个系统分配的托管标识分配到该存储帐户。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。

若要使用 Azure CLI 分配托管标识，请调用 [az storage account update](/cli/storage/account#az-storage-account-update)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="create-a-new-key-vault"></a>创建新的 Key Vault

必须为用来存储客户管理的密钥（用于 Azure 存储加密）的 Key Vault 启用两项密钥保护设置：“软删除”和“不要清除”。   若要在启用这些设置的情况下使用 PowerShell 或 Azure CLI 创建新的 Key Vault，请执行以下命令。 请记得将括号中的占位符值替换为你自己的值。 

若要使用 Azure CLI 创建新的 Key Vault，请调用 [az keyvault create](/cli/keyvault#az-keyvault-create)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>配置 Key Vault 访问策略

接下来，配置 Key Vault 的访问策略，使存储帐户有权访问 Key Vault。 此步骤使用前面分配给存储帐户的托管标识。

若要设置 Key Vault 的访问策略，请调用 [az keyvault set-policy](/cli/keyvault#az-keyvault-set-policy)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>新建密钥

接下来，在 Key Vault 中创建密钥。 若要创建密钥，请调用 [az keyvault key create](/cli/keyvault/key#az-keyvault-key-create)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>配置使用客户管理的密钥进行加密

Azure 存储加密默认使用 Microsoft 托管的密钥。 配置客户管理的密钥的 Azure 存储帐户，并指定要与存储帐户关联的密钥。

若要更新存储帐户的加密设置，请调用 [az storage account update](/cli/storage/account#az-storage-account-update)。 此示例还会查询 Key Vault URI 和密钥版本，需要使用这两个值才能将密钥与存储帐户相关联。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>更新密钥版本

创建密钥的新版本时，需将存储帐户更新为使用新版本。 首先，通过调用 [az keyvault show](/cli/keyvault#az-keyvault-show) 查询 Key Vault URI，并通过调用 [az keyvault key list-versions](/cli/keyvault/key#az-keyvault-key-list-versions) 查询密钥版本。 然后调用 [az storage account update](/cli/storage/account#az-storage-account-update) 更新存储帐户的加密设置，以使用新的密钥版本，如上一部分中所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md) 
- [什么是 Azure Key Vault？](/key-vault/key-vault-whatis)
