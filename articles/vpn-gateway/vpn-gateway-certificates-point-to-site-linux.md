---
title: 生成并导出用于点到站点的证书：Linux：CLI：Azure | Microsoft Docs
description: 使用 Linux (strongSwan) CLI 创建自签名根证书、导出公钥以及生成客户端证书。
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 01/31/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: fdb07dcbe1a18eddbb03f2436b1fae37272440bf
ms.sourcegitcommit: dcd11929ada5035d127be1ab85d93beb72909dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833137"
---
# <a name="generate-and-export-certificates"></a>生成并导出证书

点到站点连接使用证书进行身份验证。 本文介绍了如何使用 Linux CLI 和 strongSwan 创建自签名根证书并生成客户端证书。 如果要查找不同的证书说明，请参阅 [Powershell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 文章。 有关如何使用 GUI 而不是 CLI 安装 strongSwan 的信息，请参阅[客户端配置](point-to-site-vpn-client-configuration-azure-cert.md#install)一文中的步骤。

## <a name="generate-and-export"></a>生成并导出
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>后续步骤

继续执行点到站点配置以[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。