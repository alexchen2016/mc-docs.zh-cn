---
title: 使用 Azure 网络观察程序和开源工具可视化网络流量模式 | Azure
description: 本页介绍如何使用网络观察程序数据包捕获与 Capanalysis 来可视化传入和传出 VM 的流量模式。
services: network-watcher
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/22/2017
ms.date: 08/13/2018
ms.author: v-yeche
ms.openlocfilehash: 7e41596a64264b1528712749c266acba97408f08
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52661504"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>使用开源工具将传入和传出 VM 的网络流量模式可视化

数据包捕获中包含可用于执行网络取证和深度数据包检查的网络数据。 可以使用许多开源工具来分析数据包捕获，以洞察网络。 此类工具包括 CapAnalysis，它是一个开源数据包捕获可视化工具。 将数据包捕获数据可视化，是根据网络中的模式和异常快速衍生见解信息的重要方式。 此外，使用可视化效果还能以易用的方式分享这些见解。

Azure 网络观察程序允许在网络中执行数据包捕获，以提供捕获数据的功能。 本文会演练如何使用 CapAnalysis 和网络观察程序将数据包捕获可视化并从中获得见解。

## <a name="scenario"></a>方案

在 Azure 中的 VM 上部署了一个简单的 Web 应用程序，想要使用开源工具来直观显示其网络流量，以便快速识别流模式和任何潜在的异常。 使用网络观察程序可以获取网络环境的数据包捕获，并直接将它存储在存储帐户中。 然后，CapAnalysis 可以直接从存储 Blob 引入该数据包捕获并可视化其内容。

![方案][1]

## <a name="steps"></a>步骤

### <a name="install-capanalysis"></a>安装 CapAnalysis

若要在虚拟机上安装 CapAnalysis，可以参阅 https://www.capanalysis.net/ca/how-to-install-capanalysis 中的官方说明。
若要远程访问 CapAnalysis，需要通过添加一个新的入站安全规则，在 VM 上打开端口 9877。 有关在网络安全组中创建规则的详细信息，请参阅[在现有 NSG 中创建规则](../virtual-network/manage-network-security-group.md#create-a-security-rule)。 成功添加规则后，应该能够从 `http://<PublicIP>:9877` 访问CapAnalysis

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>使用 Azure 网络观察程序启动数据包捕获会话

使用网络观察程序可以捕获数据包，跟踪传入和传出虚拟机的流量。 可以参阅[使用网络观察程序管理数据包捕获](network-watcher-packet-capture-manage-portal.md)中的说明启动数据包捕获会话。 可将数据包捕获存储在 CapAnalysis 访问的存储 Blob 中。

### <a name="upload-a-packet-capture-to-capanalysis"></a>将数据包捕获上传到 CapAnalysis
使用“从 URL 导入”选项卡并提供存储数据包捕获的存储 Blob 的链接，可以直接上传网络观察程序生成的数据包捕获。

向 CapAnalysis 提供链接时，请务必在存储 Blob URL 的后面追加 SAS 令牌。  为此，请从存储帐户导航到“共享访问签名”，指定允许的权限，按“生成 SAS”按钮创建令牌。 然后，可将此 SAS 令牌追加到数据包捕获存储 Blob URL 的后面。

生成的 URL 应如以下 URL 所示： http://storageaccount.blob.core.chinacloudapi.cn/container/location?addSASkeyhere

### <a name="analyzing-packet-captures"></a>分析数据包捕获

CapAnalysis 提供多种选项来可视化数据包捕获，每种选项从不同的角度提供分析。 借助这些视觉摘要，可以了解网络流量趋势并快速查明任何异常活动。 以下列表介绍了其中的某些功能：

1. 流表

    此表提供数据包数据中的流列表、与流关联的时间戳和各种协议，以及源与目标 IP。

    ![CapAnalysis 流页][5]

1. 协议概述

    在此窗格中可以快速查看不同协议和地理区域中的网络流量分布。

    ![CapAnalysis 协议概述][6]

1. 统计信息

    在此窗格中可以查看网络流量统计信息 - 源与目标 IP 发送和接收的字节数、每个源与目标 IP 的流、各个流使用的协议，以及流的持续时间。

    ![CapAnalysis 统计信息][7]

1. Geomap

    此窗格提供网络流量的地图视图，其中使用颜色来按比例显示来自每个国家/地区的流量。 选择突出显示的国家/地区可以查看更多流统计数据，例如，该国家/地区的 IP 发送和接收的数据比例。

    ![Geomap][8]

1. 筛选器

    CapAnalysis 提供一组筛选器用于快速分析特定的数据包。 例如，可以选择按协议筛选数据，获取有关该流量子集的具体见解。

    ![筛选器][11]

    访问 [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) 可了解有关 CapAnalysis 所有功能的更多信息。

## <a name="conclusion"></a>结论

使用网络观察程序的数据包捕获功能可以捕获所需的数据来执行网络取证，以及更好地了解网络流量。 本方案说明了如何轻松将网络观察程序中的数据包捕获与开源可视化工具相集成。 使用 CapAnalysis 等开源工具将数据包捕获可视化，可以执行深度数据包检查，快速识别网络流量中的趋势。

## <a name="next-steps"></a>后续步骤

若要详细了解 NSG 流日志，请访问 [NSG 流日志](network-watcher-nsg-flow-logging-overview.md)

访问[使用 Power BI 将 NSG 流日志可视化](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->，了解如何使用 Power BI 将 NSG 流日志可视化

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png

<!--Update_Description: wording update -->