---
title:“include 文件”description:“include 文件”services: app-service author: cephalin ms.service: app-service ms.topic: "include" origin.date:10/15/2018 ms.date:12/31/2018 ms.author: v-biyu ms.custom: "include file"
---

## <a name="prepare-your-web-app"></a>准备 Web 应用

若要将自定义 SSL 证书（第三方证书或应用服务证书）绑定到 Web 应用，[应用服务计划](https://www.azure.cn/pricing/details/app-service/)必须位于“基本”、“标准”或“高级”层。 在此步骤中，请确保 Web 应用位于受支持的定价层。

### <a name="log-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.cn)。

### <a name="navigate-to-your-web-app"></a>导航到 Web 应用

在左侧菜单中单击“应用服务”，然后单击你的 Web 应用的名称。

![选择 Web 应用](./media/app-service-ssl-prepare-app/select-app.png)

你已登录到了 Web 应用的管理页面。  

### <a name="check-the-pricing-tier"></a>检查定价层

在 Web 应用页面的左侧导航窗格中，向下滚动到“设置”部分，然后选择“扩大(应用服务计划)”。

![扩展菜单](./media/app-service-ssl-prepare-app/scale-up-menu.png)

检查以确保 Web 应用不在 **F1** 或 **D1** 层中。 深蓝色的框突出显示了 Web 应用的当前层。

![检查定价层](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

**F1** 或 **D1** 层不支持自定义 SSL。 如果需要进行扩展，请遵循下一部分中的步骤。 否则，请关闭“纵向扩展”页，并跳过[纵向扩展应用服务计划](#scale-up-your-app-service-plan)部分。

### <a name="scale-up-your-app-service-plan"></a>扩展应用服务计划

选择任何非免费层（**B1**、**B2**、**B3**，或“生产”类别中的任何层）。 有关其他选项，请单击“查看其他选项”。

单击“应用” 。

![选择定价层](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

如果看到以下通知，则表示缩放操作已完成。

![扩展通知](./media/app-service-ssl-prepare-app/scale-notification.png)

