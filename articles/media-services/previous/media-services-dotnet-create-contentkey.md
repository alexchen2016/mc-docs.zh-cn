---
title: 使用 .NET 创建内容密钥
description: 了解如何创建提供对资产进行安全访问的内容密钥。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: ee40bc318269a1ffcea71fd57a1adfde5814db04
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624099"
---
# <a name="create-contentkeys-with-net"></a>使用 .NET 创建内容密钥 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

媒体服务允许创建资产和传送加密的资产。 “ContentKey”提供对“资产”的安全访问。 

创建新资产时（例如，[上传文件](media-services-dotnet-upload-files.md)之前），可以指定以下加密选项：StorageEncrypted、CommonEncryptionProtected 或 EnvelopeEncryptionProtected。 

将资产传送到客户端时，可以使用以下两个加密选项之一[将资产配置为动态加密](media-services-dotnet-configure-asset-delivery-policy.md)：DynamicEnvelopeEncryption 或 DynamicCommonEncryption。

加密的资产必须与 **ContentKey**关联。 本文介绍如何创建内容密钥。

> [!NOTE]
> 使用媒体服务 .NET SDK 创建新的“StorageEncrypted”资产时，会自动创建“ContentKey”并将其链接到资产。
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
创建内容密钥时必须设置的值之一是内容密钥类型。 选择以下值之一。 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a id="envelope_contentkey"></a>创建信封类型 ContentKey
以下代码段创建信封加密类型的内容密钥。 然后，它会将密钥与指定的资产关联。

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>创建公共类型 ContentKey
以下代码段创建公共加密类型的内容密钥。 然后，它会将密钥与指定的资产关联。

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```
