---
title: 使用 Power BI 连接到 Azure Analysis Services | Azure
description: 了解如何使用 Power BI 连接到 Azure Analysis Services 服务器。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 01/09/2019
ms.date: 01/28/2019
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: 30d0285f25329de0b409fdee2b4812f4ddf2b14f
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625391"
---
# <a name="connect-with-power-bi"></a>使用 Power BI 进行连接

在 Azure 中创建服务器并向其部署表格模型后，组织中的用户便可以连接并开始浏览数据。 

> [!TIP]
> 请务必使用最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)。
> 
> 

## <a name="connect-in-power-bi-desktop"></a>在 Power BI Desktop 中连接

1. 在 Power BI Desktop 中，单击“获取数据” > “Azure” > “Azure Analysis Services 数据库”。

2. 在“服务器”中，输入服务器名称。 请务必包括完整的 URL，例如，asazure://chinanorth.asazure.chinacloudapi.cn/advworks。

   <!-- Not Available on China East -->
3. 在“数据库”中，如果知道要连接到的表格模型数据库或透视的名称，请将其粘贴在此处。 如果不知道，可以将此字段留空，并在稍后选择数据库或透视。

4. 选择连接选项，然后按“连接”。 

    同时支持“实时连接”和“导入”选项。 但是，我们建议你使用实时连接，因为导入模式确实存在一些限制；最重要的是，导入过程中可能会影响服务器性能。 此外，如果要在 Power BI 服务中刷新模型，仅当选择“实时连接”时，“允许从 Power BI 访问”设置才适用。

5. 如果出现提示，请输入登录凭据。 

6. 在**导航器**中，展开服务器，选择要连接到的模型或透视，并单击“连接”。 单击模型或透视可显示该视图的所有对象。

    Power BI Desktop 中会打开模型，并且在“报表”视图中显示空白报表。 “字段”列表中会显示所有非隐藏的模型对象。 连接状态将显示在右下角。

## <a name="connect-in-power-bi-service"></a>在 Power BI（服务）中进行连接

1. 在服务器上创建一个与模型具有实时连接的 Power BI Desktop 文件。
2. 在 [Power BI](https://powerbi.microsoft.com) 中，单击“获取数据” > “文件”，然后找到 .pbix 文件并选择该文件。

## <a name="see-also"></a>另请参阅
[连接到 Azure Analysis Services](analysis-services-connect.md)   
[客户端库](analysis-services-data-providers.md)

<!--Update_Description: update meta properties, wording update -->