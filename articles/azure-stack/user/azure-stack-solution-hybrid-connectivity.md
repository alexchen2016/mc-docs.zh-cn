---
title: 使用 Azure 和 Azure Stack 配置混合云连接 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 配置混合云连接。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
origin.date: 01/14/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f7a7f8800a6bcd30a2f24acebb4bdfbb590e5eb4
ms.sourcegitcommit: 9642fa6b5991ee593a326b0e5c4f4f4910f50742
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64854609"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>教程：使用 Azure 和 Azure Stack 配置混合云连接

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用混合连接模式在公有云 Azure 和 Azure Stack 中安全地访问资源。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 按照隐私或法规要求将数据存储在本地，但同时可以访问公有云 Azure 资源。
> - 在公有云 Azure 中使用云规模的应用部署和资源的同时，保留旧版系统。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> 白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)回顾了设计、部署和运行混合应用程序所需的软件质量的要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。


## <a name="prerequisites"></a>先决条件

生成混合连接部署需要一些组件。 这其中的某些组件需要时间来准备，因此必须进行相应的计划。

**Azure Stack**

Azure OEM/硬件合作伙伴可以部署生产型 Azure Stack，所有用户都可以部署 Azure Stack 开发工具包 (ASDK)。

**Azure Stack 组件**

Azure Stack 操作员必须部署应用服务、创建计划和套餐、创建租户订阅，并添加 Windows Server 2016 映像。 如果已经有其中的某些组件，请确保它们符合要求，然后开始本教程。

本教程假设你对 Azure 和 Azure Stack 有一些基本的了解。 若要在开始本教程之前了解更多信息，请阅读以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - 如果没有 Azure 订阅，可在开始前创建一个 [试用帐户](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F) 。
 - 在 Azure 中创建  [Web 应用](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts)。 请记下 Web 应用 URL，因为需在本教程中使用它。

### <a name="azure-stack"></a>Azure Stack

 - 使用生产型 Azure Stack，或部署 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 提供的 Azure Stack 开发工具包。
   >[!Note]
   >部署 ASDK 可能需要长达 7 小时的时间，因此请进行相应的计划。

 - 将 [应用服务](../operator/azure-stack-app-service-deploy.md) PaaS 服务部署到 Azure Stack。
 - 在 Azure Stack 环境中[创建计划和套餐](../operator/azure-stack-plan-offer-quota-overview.md)。
 - 在 Azure Stack 环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

### <a name="before-you-begin"></a>准备阶段

在开始配置混合云连接之前，请验证自己是否符合以下条件：

 - 需要一个用于 VPN 设备的面向外部的公共 IPv4 地址。 此 IP 地址不得位于 NAT 之后。
 - 所有资源都部署在同一区域/位置。

#### <a name="tutorial-example-values"></a>教程示例值

本教程中的示例使用以下值。 可使用这些值创建测试环境，或根据这些值来更好地理解示例。 有关常规 VPN 网关设置的详细信息，请参阅 [关于 VPN 网关设置](/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

连接规范：

 - **VPN 类型**： 基于路由
 - **连接类型**： 站点到站点 (IPsec)
 - **网关类型**： VPN
 - **Azure 连接名称**： Azure-Gateway-AzureStack-S2SGateway（门户会自动填充此值）
 - **Azure Stack 连接名称**： AzureStack-Gateway-Azure-S2SGateway（门户会自动填充此值）
 - **共享密钥**： 任何与 VPN 硬件兼容且在连接的两端都有匹配值的共享密钥
 - **订阅**： 任何首选订阅
 - **资源组**：Test-Infra

网络和子网 IP 地址：

| Azure/Azure Stack 连接 | Name | 子网 | IP 地址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虚拟网关 | Azure-Gateway |  |  |
| Azure Stack 虚拟网关 | AzureStack-Gateway |  |  |
| Azure 公共 IP | Azure-GatewayPublicIP |  | 在创建时确定 |
| Azure Stack 公共 IP | AzureStack-GatewayPublicIP |  | 在创建时确定 |
| Azure 本地网关 | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 公共 IP 值 |
| Azure Stack 本地网关 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 公共 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>在公有云 Azure 和 Azure Stack 中创建虚拟网络

请执行以下步骤，以便通过门户创建虚拟网络。 如果将本文用作教程，则可使用这些 [示例值](/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) 。 但是，如果根据本文来配置生产型环境，请将示例设置替换为你自己的值。

> [!IMPORTANT]
> 必须确保在 Azure 或 Azure Stack vNet 地址空间中没有 IP 地址重叠现象。

若要在 Azure 中创建 VNet，请执行以下操作：

1. 使用浏览器连接到 [Azure 门户](https://portal.azure.cn/) 并使用 Azure 帐户进行登录。
2. 选择“创建资源”。 **** 在“在市场中搜索”字段中输入 `virtual network`。 ****  在结果列表中找到“虚拟网络”，然后选择“虚拟网络”。 ****  ****
3. 从“选择部署模型”列表中选择“资源管理器”，然后选择“创建”。  **** ****
4. 在“创建虚拟网络”中配置 VNet 设置。 必填字段名称带有红色星号前缀。  输入有效值时，该星号变为绿色复选标记。

若要在 Azure Stack 中创建 VNet，请执行以下操作：

* 使用 Azure Stack **租户门户**重复前述步骤 (1-4)。

## <a name="add-a-gateway-subnet"></a>添加网关子网

将虚拟网络连接到网关之前，需创建要连接的虚拟网络的网关子网。 网关服务使用在网关子网中指定的 IP 地址。

在  [Azure 门户](https://portal.azure.cn/)中，导航到要在其中创建虚拟网关的资源管理器虚拟网络。

1. 选择 vNet，以便打开“虚拟网络”页。
2. 在“设置” **** 中，选择“子网”。 ****
3. 在“子网”页中选择“+网关子网”，以打开“添加子网”页面。 ****  ****  ****

    ![添加网关子网](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. 子网的 **名称** 自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。
5. 更改按配置要求提供的“地址范围”值，然后选择“确定”。 ****

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建虚拟网关

请执行以下步骤，以便在 Azure 中创建虚拟网关。

1. 在门户页左侧选择 **+** ，然后在搜索字段中输入“虚拟网关”。
2. 在“结果” **** 中，选择“虚拟网关” ****。
3. 在“虚拟网关”中选择“创建”，以打开“创建虚拟网关”页面。 **** **** 
4. 在“创建虚拟网关”中指定网络网关的值，如**教程示例值**中所示，然后指定下述其他值：

   - **SKU**：基本
   - **虚拟网络**：选择此前创建的虚拟网络。 已创建的网关子网会自动选中。
   - **第一个 IP 配置**：这是网关的公共 IP。
     - 选择“创建网关 IP 配置”，然后就会转到“选择公共 IP 地址”页。
     - 选择“+新建”，打开“创建公共 IP 地址”页。
     - 输入公共 IP 地址的“名称”。 将“SKU”保留为“基本”，然后选择“确定”，以便保存所做的更改。

       > [!Note]
       > 目前，VPN 网关仅支持动态公共 IP 地址分配。 但是，这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 VPN 网关进行大小调整、重置或其他内部维护/升级不会更改 IP 地址。

5. 验证网关设置。
6. 选择“创建”，创建 VPN 网关。 将会验证网关设置，并会在仪表板上显示“正在部署虚拟网关”磁贴。

   >[!Note]
   >创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

    创建网关后，可以通过在门户中查看虚拟网络，来查看分配给网关的 IP 地址。 网关显示为连接的设备。 若要查看网关的详细信息，请选择设备。

7. 在 Azure Stack 部署上重复前述步骤 (1-5)。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建本地网关

本地网络网关通常是指本地位置。 请为站点提供一个可供 Azure 或 Azure Stack 引用的名称，然后指定以下配置：

- 要为其创建连接的本地 VPN 设备的 IP 地址。
- 可以通过 VPN 网关将其路由到 VPN 设备的 IP 地址前缀。 指定的地址前缀是位于本地网络的前缀。

  >[!Note]
  >如果之后本地网络发生了更改，或需要更改 VPN 设备的公共 IP 地址，可轻松更新这些值。

1. 在门户中，选择“+创建资源”。 ****
2. 在搜索框中输入“本地网关”，然后按 **Enter** 进行搜索。 **** 这会返回一个结果列表。
3. 选择“本地网关”，然后选择“创建”，打开“创建本地网关”页。 ****  **** 
4. 使用**教程示例值**，在“创建本地网关”上指定本地网关的值。 包括以下其他值。

    - **IP 地址**： 这是想要 Azure 或 Azure Stack 连接到的 VPN 设备的公共 IP 地址。 指定一个有效的不在 NAT 后面的公共 IP 地址，这样 Azure 就能访问该地址。 如果目前没有 IP 地址，可以使用示例中显示的作为占位符的值，但是需要返回来将占位符替换为 VPN 设备的公共 IP 地址。 除非提供有效的地址，否则 Azure 无法连接到设备。
    - **地址空间**： 这是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保指定的范围没有与要连接到的其他网络的范围重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请使用自己的值而不是示例值。
    - **配置 BGP 设置**： 仅在配置 BGP 时使用。 否则，不选择此项。
    - **订阅**： 确保显示的是正确订阅。
    - **资源组**： 选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
    - **位置**： 选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。
5. 将所需值指定完以后，选择“创建”即可创建本地网关。 
6. 在 Azure Stack 部署上重复这些步骤 (1-5)。

## <a name="configure-your-connection"></a>配置连接

通过站点到站点连接连接到本地网络需要 VPN 设备。 配置的 VPN 设备称为“连接”。 若要配置连接，需提供：

- 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
- 虚拟网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 Azure 门户查找 VPN 网关的公共 IP 地址，请导航到“虚拟网关”，然后选择网关的名称。

执行以下步骤，在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。

1. 在 Azure 门户中，选择“+创建资源”。 ****
2. 搜索**连接**。
3. 在“结果”中选择“连接”。 ****
4. 在“连接”上选择“创建”。
5. 在“创建连接”上配置以下设置：

    - **连接类型**： 选择“站点到站点(IPSec)”。
    - **资源组**：选择你的测试资源组。
    - **虚拟网络网关**：选择已创建的虚拟网络网关。
    - **本地网络网关**：选择已创建的本地网络网关。
    - **连接名称**：系统会使用两个网关的值自动填充此项。
    - **共享密钥**： 此值必须与用于本地 VPN 设备的值匹配。 教程示例使用“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此值必须与配置 VPN 设备时指定的值相同。
    - “订阅”、“资源组”和“位置”值是固定的。

6. 选择“确定”以创建连接。

可在虚拟网关的“连接”页中查看连接。 ****  “状态”会从“未知”转换为“正在连接”，再转换为“成功”。 **  ** **

<!-- Update_Description: wording update -->

