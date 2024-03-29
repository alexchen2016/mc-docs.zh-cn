---
title: 有关订购 Azure Data Box 的教程 | Microsoft Docs
description: 了解部署先决条件以及如何订购 Azure Data Box
services: databox
author: WenJason
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
origin.date: 04/23/2019
ms.date: 06/10/2019
ms.author: v-jay
ms.openlocfilehash: c30ca847a0b1885d185556d3ec00d322cbe3338b
ms.sourcegitcommit: 67a78cae1f34c2d19ef3eeeff2717aa0f78de38e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726494"
---
# <a name="tutorial-order-azure-data-box"></a>教程：订购 Azure Data Box

Azure Data Box 是一个混合解决方案，可用于快速、方便、可靠地将本地数据导入 Azure 中。 请先将数据传输到 Microsoft 提供的 80 TB（可用容量）存储设备，然后将设备寄回。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box。 本教程的介绍内容包括：

> [!div class="checklist"]
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

## <a name="prerequisites"></a>先决条件

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备。

### <a name="for-service"></a>对于服务

在开始之前，请确保：
- 具有 Azure 存储帐户和访问凭据。
- 确保用于 Data Box 服务的订阅是下述类型之一：
    - Microsoft 企业协议 (EA)。 详细了解 [EA 订阅](https://azure.cn/pricing/enterprise-agreement/)。
    - Microsoft Azure 赞助。 详细了解 [Azure 赞助计划](https://azure.microsoft.com/offers/ms-azr-0036p/)。

- 确保有订阅的所有者或参与者访问权限，以便创建 Data Box 订单。

### <a name="for-device"></a>对于设备

在开始之前，请确保：
- 应该将一个主机连接到数据中心网络。 Data Box 会从此计算机复制数据。 主机必须按照 [Azure Data Box 系统要求](data-box-system-requirements.md)中的说明运行支持的操作系统。
- 数据中心需要有高速网络。 强烈建议你至少使用一个 10 GbE 连接。 如果 10 GbE 连接不可用，则可使用 1 GbE 数据链路，但复制速度会受影响。


## <a name="order-data-box"></a>订购 Data Box

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.cn)。
2. 单击“+ 创建资源”并搜索 *Azure Data Box*。  单击“Azure Data Box”。 
    
   [![搜索“Azure Data Box 1”](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. 单击**创建**。

4. 检查 Data Box 服务是否在你的区域可用。 输入或选择以下信息，然后单击“应用”。  

    |设置  |Value  |
    |---------|---------|
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 该订阅将链接到计费帐户。       |
    |传输类型     | 选择“导入到 Azure”。         |
    |来源国家/地区     |   选择数据当前所在的国家/地区。         |
    |目标 Azure 区域     |     选择要在其中传输数据的 Azure 区域。        |

5. 选择“Data Box”。  单次订购的可用最大容量为 80 TB。 可以创建多个订单，以增加数据大小。

      [![选择 Data Box 选项 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. 在“订单”中指定“订单详细信息”。   输入或选择以下信息，然后单击“下一步”。 
    
    |设置  |值  |
    |---------|---------|
    |Name     |  提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。      |
    |资源组     |   使用现有资源组，或创建新的资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。         |
    |目标 Azure 区域     | 选择存储帐户的区域。 <br> 有关详细信息，请转到[适用区域](data-box-overview.md#region-availability)。        |
    |存储目标     | 选择存储帐户和/或托管磁盘。 <br> 根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个或多个存储帐户。 Data Box 可以与最多 10 个存储帐户链接。 <br> 也可以创建新的**常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。 <br>支持使用虚拟网络的存储帐户。 若要允许 Data Box 服务使用受保护的存储帐户，请在存储帐户网络防火墙设置中启用受信任的服务。 有关详细信息，请参阅如何[将 Azure Data Box 添加为受信任的服务](../storage/common/storage-network-security.md#exceptions)。|

    如果使用存储帐户作为存储目标，则会看到以下屏幕截图：

    ![存储帐户的 Data Box 订单](media/data-box-deploy-ordered/order-storage-account.png)

    如果使用 Data Box 从本地 VHD 创建托管磁盘，则还需要提供以下信息：

    |设置  |Value  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 当 Data Box 服务创建托管磁盘的 Data Box 订单时，仅当事先已创建资源组，你才可以使用现有的资源组。 <br> 可以指定多个资源组并用分号分隔。 最多支持 10 个资源组。|

    ![托管磁盘的 Data Box 订单](media/data-box-deploy-ordered/order-managed-disks.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 作为页 Blob 上传到临时存储帐户，然后将其转换为托管磁盘并转移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

7. 在“寄送地址”中，提供你的姓名、公司的名称和邮政地址，以及有效的电话号码。  单击“验证地址”。  服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。 单击“下一步”  。

8. 在“通知详细信息”中，指定电子邮件地址。  服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

9. 查看与订单、联系人、通知和隐私条款相关的信息**摘要**。 选中对应于同意隐私条款的复选框。

10. 单击“订购”。  创建订单需要几分钟时间。


## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box 订单，然后转到“概述”来查看状态。  门户中会显示订单处于“已订购”状态。 

如果设备缺货，你会收到通知。 如果设备有货，Azure 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

- 将为与设备关联的每个存储帐户创建 SMB 共享。
- 将为每个共享生成访问凭据（例如用户名和密码）。
- 也会生成用于解锁设备的设备密码。
- 锁定 Data Box 的目的是在任何时候防止对设备进行未经授权的访问。

设备准备完成后，门户会显示订单处于“已处理”状态。 

![处理的 Data Box 订单](media/data-box-overview/data-box-order-status-processed.png)

然后，Azure 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。 

![派遣的 Data Box 订单](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中单击“取消”。  

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。
 
若要删除已取消的订单，请转到“概况”，然后在命令栏中单击“删除”。  

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

请继续学习下一篇教程，了解如何设置 Data Box。

> [!div class="nextstepaction"]
> [设置 Azure Data Box](./data-box-deploy-set-up.md)


