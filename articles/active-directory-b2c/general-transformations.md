---
title: Azure Active Directory B2C 标识体验框架架构的常规声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的常规声明转换示例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
origin.date: 09/10/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 2e65a6a6c746891847352f6c64f33af4f24ba860
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004252"
---
# <a name="general-claims-transformations"></a>常规声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示了在 Azure Active Directory (Azure AD) B2C 中使用标识体验框架架构的常规声明转换的过程。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="doesclaimexist"></a>DoesClaimExist

检查 inputClaim 是否存在并将 outputClaim 相应地设置为 true 或 false。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |任意 | 需要验证是否存在的输入声明。 |
| OutputClaim | outputClaim | 布尔值 | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换检查声明是否存在或是否包含任何值。 返回值是指示声明是否存在的布尔值。 以下示例检查电子邮件地址是否存在。

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**: someone@contoso.com
- 输出声明： 
    - **outputClaim**: true

## <a name="hash"></a>哈希

使用加密盐和机密对提供的纯文本执行哈希。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 纯文本 | 字符串 | 要加密的输入声明 |
| InputClaim | 加密盐 | 字符串 | 加密盐参数。 可以使用 `CreateRandomString` 声明转换创建随机值。 |
| InputParameter | randomizerSecret | 字符串 | 指向现有的 Azure AD B2C 策略密钥。 若要新建一个：在 Azure AD B2C 租户中，请选择“B2C 设置 > 标识体验框架”。 选择“策略密钥”，查看租户中的可用密钥。 选择“设置” （应用程序对象和服务主体对象）。 对于“选项”，请选择“手动”。 提供名称（可能会自动添加前缀 B2C_1A_。）。 在“机密”框中，输入要使用的任何机密，如 1234567890。 对于“密钥用法”，请选择“机密”。 选择“创建” 。 |
| OutputClaim | hash | 字符串 | 调用此声明转换后生成的 ClaimType。 在 `plaintext` inputClaim 中配置的声明。 |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **plaintext**: MyPass@word1
    - **加密盐**：487624568
    - **randomizerSecret**：B2C_1A_AccountTransformSecret
- 输出声明： 
    - **outputClaim**：CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=




