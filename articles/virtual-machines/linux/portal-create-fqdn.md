---
title: 在 Azure 门户中为 Linux VM 创建 FQDN | Azure
description: 了解如何在 Azure 门户中为基于 Resource Manager 的虚拟机创建完全限定域名或 FQDN。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
origin.date: 08/15/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c5502dd83ab30e200e8362e2016e8664855d216
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674355"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.cn)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以[在门户中创建 VM](quick-create-portal.md)，也可以[使用 Azure CLI 创建 VM](quick-create-cli.md)。 在 VM 正常运行后，请按照以下步骤操作：

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

现在可以使用此 DNS 名称（例如搭配 `ssh azureuser@mydns.chinanorth.cloudapp.chinacloudapi.cn`）远程连接至 VM。
<!-- cloudapp.azure.com to cloudapp.chinacloudapi.cn is Correct -->

## <a name="next-steps"></a>后续步骤
VM 已经有公共 IP 和 DNS 名称，现在可以部署通用应用程序框架或服务，例如 nginx、MongoDB、Docker 等等。

也可以深入了解[使用 Resource Manager](../../azure-resource-manager/resource-group-overview.md)，获取生成 Azure 部署的相关提示。

<!--Update_Description: update meta properties -->