---
title: 为 Azure 备份配置报表
description: 使用恢复服务保管库为 Azure 备份配置 Power BI 报表。
services: backup
author: lingliw
manager: digimobile
ms.service: backup
ms.topic: conceptual
ms.date: 04/12/19
ms.author: v-lingwu
ms.openlocfilehash: 3ad142b750dab3c68f7083fcf9844a70229fb7c1
ms.sourcegitcommit: f9d082d429c46cee3611a78682b2fc30e1220c87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59566367"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表
本文介绍使用恢复服务保管库为 Azure 备份配置报表所需执行的步骤。 另外还介绍如何通过 Power BI 访问报表。 完成这些步骤后，可直接转到 Power BI，以便查看、自定义和创建报表。

> [!IMPORTANT]
> 从 2018 年 11 月 1 日起，某些客户可能会在 Power BI 的 Azure 备份应用中加载数据时发现问题，其消息指出“我们在 JSON 输入末尾发现额外字符。 此异常由 IDataReader 接口引起。”
这是由于在将数据加载到存储帐户中时，其格式发生了变化。
请下载最新的应用（版本 1.8）以避免此问题。
>
>

## <a name="supported-scenarios"></a>支持的方案
- 使用 Azure 恢复服务代理进行 Azure 虚拟机备份以及将文件和文件夹备份到云时，支持 Azure 备份报表。
- 目前，Azure SQL 数据库、Azure 文件共享、Data Protection Manager 和 Azure 备份服务器不支持报表。
- 如果为每个保管库配置同一存储帐户，可以跨保管库和订阅查看报表。 所选存储帐户必须位于恢复服务保管库所在的区域。
- 在 Power BI 中，报表按计划每 24 小时刷新一次。 也可在 Power BI 中临时刷新报表。 在这种情况下，会使用客户存储帐户中的最新数据来呈现报表。

## <a name="prerequisites"></a>先决条件
- 创建 [Azure 存储帐户](../storage/common/storage-quickstart-create-account.md)，以便为报表配置此帐户。 此存储帐户用于存储与报表相关的数据。
- [创建 Power BI 帐户](https://powerbi.microsoft.com/landing/signin/)，以便可以使用 Power BI 门户查看、自定义并创建自己的报表。
- 注册资源提供程序 Microsoft.insights（如果尚未注册）。 将订阅用于存储帐户和恢复服务保管库，以便报表数据可以流向存储帐户。 要执行此步骤，请转到 Azure 门户，选择“订阅” > “资源提供程序”，并找到此提供程序以进行注册。

## <a name="configure-storage-account-for-reports"></a>配置报表的存储帐户
请按以下步骤操作，使用 Azure 门户配置恢复服务保管库的存储帐户。 这是一次性配置。 配置存储帐户后，可以直接转到 Power BI 来查看内容包并使用报表。

1. 如果已打开恢复服务保管库，请转到下一步。 如果未打开恢复服务保管库，则请在 Azure 门户中选择“所有服务”。

   * 在资源列表中，输入“恢复服务”。
   * 开始键入时，会根据输入筛选该列表。 出现“恢复服务保管库”时，请选择它。
   * 此时显示恢复服务保管库列表。 在恢复服务保管库列表中选择一个保管库。

     此时会打开选定的保管库仪表板。
2. 在保管库下显示的项列表中，选择“监视和报表”部分下的“备份报表”，以配置报表的存储帐户。

      ![选择“备份报表”菜单项 - 第 2 步](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. 在“备份报表”边栏选项卡上，选择“诊断设置”链接。 此链接将打开用于将数据推送到客户存储帐户的**诊断设置** UI。

      ![启用诊断 - 第 3 步](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. 选择“启用诊断”，打开用于配置存储帐户的 UI。

      ![启用诊断 - 第 4 步](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. 在“名称”框中，输入设置名称。 选择“存档到存储帐户”复选框，这样报表数据就可以开始流向存储帐户。

      ![启用诊断 - 第 5 步](./media/backup-azure-configure-reports/select-setting-name.png)
6. 选择“存储帐户”，接着从列表中选择用于存储报表数据的相关订阅和存储帐户，再选择“确定”。

      ![选择存储帐户 - 第 6 步](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. 在“日志”部分选中“AzureBackupReport”复选框。 移动滑块，选择此报表数据的保持期。 存储帐户中报表数据的保持期即为使用此滑块选择的时间段。

      ![保存存储帐户 - 第 7 步](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. 查看所有更改，然后选择“保存”。 此操作可确保保存所有更改，现在用于存储报表数据的存储帐户已配置完成。

9. “诊断设置”表现在显示为保管库启用的新设置。 如果未显示，请刷新该表以查看更新的设置。

      ![查看诊断设置 - 第 9 步](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> 通过保存存储帐户完成对报表的配置后，请等待 24 小时，完成初始数据推送。 仅在此之后才将 Azure 备份应用导入 Power BI。 有关详细信息，请参阅[“常见问题解答”部分](#frequently-asked-questions)。 
>
>

## <a name="view-reports-in-power-bi"></a>在 Power BI 中查看报表 
使用恢复服务保管库配置报表的存储帐户后，报表数据大约需要 24 小时才会开始流入。 设置存储帐户 24 小时后，请按下列步骤操作，在 Power BI 中查看报表。
如果要自定义和共享报表，请创建工作区并执行以下步骤

1. [登录](https://powerbi.microsoft.com/landing/signin/) Power BI。
2. 选择“获取数据”。 在“用于创建自己内容的更多方法”中，选择“服务内容包”。 按照 [Power BI 文档](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/)中的步骤连接到服务。

3. 在“搜索”栏中输入“Azure 备份”，然后选择“立即获取”。

      ![获取内容包](./media/backup-azure-configure-reports/content-pack-get.png)
4. 输入在上面的步骤 5 中配置的存储帐户的名称，然后选择“下一步”。

    ![输入存储帐户名称](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. 使用身份验证方法“密钥”，输入此存储帐户的存储帐户密钥。 若要[查看并复制存储访问密钥](../storage/common/storage-account-manage.md#access-keys)，请在 Azure 门户中转到存储帐户。 

     ![输入存储帐户](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. 选择“登录”。 成功登录后，会看到一条导入数据通知。

    ![正在导入内容包](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    导入完成后，会看到一条“成功”通知。 如果存储帐户中的数据量很大，则内容包导入时间可能会稍长一点。
    
    ![成功导入内容包](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. 成功导入数据后，“Azure 备份”内容包便会显示在导航窗格的“应用”中。 在“仪表板”、“报表”和“数据集”下，该列表现在显示了 Azure 备份。

8. 选择“仪表板”下的“Azure 备份”，显示一组固定的关键报表。

      ![Azure 备份仪表板](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. 若要查看一组完整报表，请选择仪表板中的任意报表。

      ![Azure 备份“作业运行状况”选项卡](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. 选择报表中的各个选项卡，查看相应区域的报表。

      ![Azure 备份报表选项卡](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>如何确定报表数据是否已开始流向存储帐户？
转到配置的存储帐户，并选择容器。 如果容器包含 insights-logs-azurebackupreport 条目，则表示报表数据已开始流向存储帐户。

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>数据多久向存储帐户和 Power BI 中的 Azure 备份内容包推送一次？
  在用户配置存储帐户后，数据大约需要 24 小时才能推送到存储帐户。 在这一次初始推送完成后，将按下图中所示频率刷新数据。

  * 与“作业”、“警报”、“备份项”、“保管库”、“受保护的服务器”和“策略”相关的数据：在客户登录时推送到客户存储帐户。

  * 与“存储”相关的数据：每 24 小时推送到客户存储帐户一次。

       ![Azure 备份报表数据推送频率](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI [计划每天刷新一次](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)。 对于内容包，可以在 Power BI 中手动刷新数据。

### <a name="how-long-can-i-retain-reports"></a>报表可以保留多长时间？
配置存储帐户时，可以在存储帐户中选择报表数据的保持期。 请按照[配置报表的存储帐户](backup-azure-configure-reports.md#configure-storage-account-for-reports)部分中的步骤 6 执行操作。 此外，还可以[在 Excel 中分析报表](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)，并保存报表以根据需要延长保持期。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>配置存储帐户后，报表中是否会显示我的所有数据？
 配置存储帐户后生成的所有数据都会推送到存储帐户，并会显示在报表中。 不会为报表推送正在进行的作业。 作业完成或失败后，会将其发送到报表。

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>如果我已配置存储帐户来查看报表，能否更改配置以使用其他存储帐户？
能，可以将配置更改为指向不同的存储帐户。 连接到 Azure 备份内容包时，可使用新配置的存储帐户。 此外，配置其他存储帐户后，新数据便会流向该存储帐户。 更改配置前的旧数据仍会保留在旧存储帐户中。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>能否跨保管库和订阅查看报表？
能，可以跨各种保管库配置同一存储帐户，以便跨保管库查看报表。 此外，还可以跨订阅为保管库配置同一存储帐户。 然后，可以在连接到 Power BI 中的 Azure 备份内容包时使用此存储帐户来查看报表。 所选存储帐户必须位于恢复服务保管库所在的区域。

## <a name="troubleshooting-errors"></a>排查错误
| 错误详细信息 | 解决方法 |
| --- | --- |
| 为备份报表设置存储帐户后，“存储帐户”仍显示“未配置”。 | 如果已成功配置存储帐户，则即使存在此问题，报表数据仍会流入该帐户。 若要解决此问题，请转到 Azure 门户并选择“所有服务” > “诊断设置” > “恢复服务保管库” > “编辑设置”。 删除以前配置的设置，然后在同一边栏选项卡中创建新设置。 这次请在“名称”框中选择“服务”。 现在，应会显示配置的存储帐户。 |
|在 Power BI 中导入 Azure 备份内容包后，会显示“404 - 未找到容器”错误消息。 | 如上所述，在恢复服务保管库中配置报表后，必须等待 24 小时，Power BI 中才会正确显示报表。 如果在 24 小时内尝试访问报表，会显示此错误消息，因为尚不存在完整的数据，无法显示有效报表。 |

## <a name="next-steps"></a>后续步骤
配置存储帐户并导入 Azure 备份内容包后，需在后续步骤中自定义报表，并使用报表数据模型创建报表。 有关详细信息，请参阅以下文章。

* [使用 Azure 备份报表数据模型](backup-azure-reports-data-model.md)
* [在 Power BI 中筛选报表](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [在 Power BI 中创建报表](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)


