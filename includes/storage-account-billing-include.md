---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 10/19/2018
ms.date: 12/10/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f2c61a1fb1122d60b3fa41f79f167056e84d7310
ms.sourcegitcommit: 5f2849d5751cb634f1cdc04d581c32296e33ef1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53029285"
---
我们会根据存储帐户使用情况，对 Azure 存储计费。 存储帐户中的所有对象会作为组共同计费。 

存储成本根据以下因素进行计算：区域/位置、帐户类型、访问层、存储容量、复制方案、存储事务和数据流出量。

* **区域**是指帐户所基于的地理区域。
* **帐户类型**是指所使用的存储帐户类型。 
* **访问层**是指你为常规用途 v2 或 Blob 存储帐户指定的数据使用模式。
* 存储**容量**指的是存储帐户中用来存储数据的配额。
* **复制**可以确定一次保留的数据副本的数量以及保留位置。
* **事务**是指对 Azure 存储的所有读取和写入操作。
* **数据流出量**是指传出某个 Azure 区域的任何数据。 当不在同一区域中的应用程序访问存储帐户中的数据时，需要为数据流出量付费。

[Azure 存储定价](https://www.azure.cn/pricing/details/storage/) 页提供基于帐户类型、存储容量、复制和交易的详细定价信息。 [数据传输定价详细信息](https://www.azure.cn/pricing/details/data-transfer/) 提供了针对数据流出量的详细定价信息。 可以使用 [Azure 存储定价计算器](https://www.azure.cn/pricing/calculator/?scenario=data-management) 来帮助估算成本。

