---
title: 教程 - 使用 Azure 门户创建托管多个网站的应用程序网关
description: 本教程介绍了如何使用 Azure 门户创建托管多个网站的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
origin.date: 04/18/2019
ms.date: 05/20/2019
ms.author: v-junlch
ms.openlocfilehash: 060b6e9267dbc0d3165f5ccc1eeb0a549d9cca56
ms.sourcegitcommit: dc0db00da570f0c57f4a1398797fc158a2c423c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65960920"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>教程：使用 Azure 门户创建和配置托管多个网站的应用程序网关

创建[应用程序网关](overview.md)时，可以使用 Azure 门户[配置多个网站的托管](multiple-site-overview.md)。 本教程使用虚拟机定义后端地址池。 然后，基于所拥有的域配置侦听器和规则，以确保 Web 流量可到达池中的相应服务器。 本教程假定你拥有多个域，并使用示例 *www.contoso.com* 和 *www.fabrikam.com*。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建应用程序网关
> * 为后端服务器创建虚拟机
> * 使用后端服务器创建后端池
> * 创建后端侦听器
> * 创建路由规则
> * 在域中创建 CNAME 记录

![多站点路由示例](./media/create-multiple-sites-portal/scenario.png)

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 [https://portal.azure.cn](https://portal.azure.cn) 登录到 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在创建的资源之间实现通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 单击 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
3. 输入应用程序网关的以下值：

   - *myAppGateway* - 应用程序网关的名称。
   - *myResourceGroupAG* - 新资源组。

     ![新建应用程序网关](./media/create-multiple-sites-portal/application-gateway-create.png)

4. 接受其他设置的默认值，然后单击“确定”。
5. 依次单击“选择虚拟网络”、“新建”，然后输入虚拟网络的以下值：

   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。

     ![创建虚拟网络](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. 单击“确定”创建虚拟网络和子网。
7. 依次单击“选择公共 IP 地址”、“新建”，然后输入公共 IP 地址的名称。 在本示例中，公共 IP 地址名为 *myAGPublicIPAddress*。 接受其他设置的默认值，然后单击“确定”。
8. 接受侦听器配置的默认值，让 Web 应用程序防火墙保留禁用状态，然后单击“确定”。
9. 检查摘要页上的设置，然后单击“确定”创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 单击左侧菜单中的“所有资源”，然后从资源列表中单击“myVNet”。
2. 单击“子网”，然后单击“子网”。

    ![创建子网](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后单击“确定”。

## <a name="create-virtual-machines"></a>创建虚拟机

在此示例中，将创建两个虚拟机以用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS 以验证流量是否正确路由。

1. 单击“新建” 。
2. 单击“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 输入虚拟机的以下值：

    - *contosoVM* - 虚拟机的名称。
    - *azureuser* - 管理员用户名。
    - *Azure123456!* - 密码。
    - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。

4. 单击 **“确定”** 。
5. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。 
7. 单击“禁用”以禁用启动诊断。
8. 创建“确定”，检查“摘要”页上的设置，然后单击“创建”。

### <a name="install-iis"></a>安装 IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 在 PowerShell 中使用以下命令登录到 Azure 门户：

    ```powershell
    Connect-AzAccount -Environment AzureChinaCloud
    ```

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location chinanorth `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 在 Set-AzVMExtension 中输入 *fabrikamVM* 作为名称，并输入 VMName 值。

## <a name="create-backend-pools-with-the-virtual-machines"></a>使用虚拟机创建后端池

1. 单击“所有资源”，然后单击“myAppGateway”。
2. 依次单击“后端池”、“添加”。
3. 输入名称 *contosoPool*，并使用“添加目标”添加 *contosoVM*。

    ![添加后端服务器](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. 单击 **“确定”** 。
5. 依次单击“后端池”、“添加”。
6. 使用刚刚完成的步骤创建 *fabrikamPool* 与 *fabrikamVM*。

## <a name="create-backend-listeners"></a>创建后端侦听器

1. 单击“侦听器”，然后单击“多站点”。
2. 为侦听器输入以下值：
    
   - *contosoListener* - 作为侦听器的名称。
   - *www.contoso.com* - 将此主机名示例替换为自己的域名。

3. 单击 **“确定”** 。
4. 使用名称 *fabrikamListener* 并使用第二个域名创建第二个侦听器。 在此示例中，使用 *www.fabrikam.com*。

![多站点侦听器](./media/create-multiple-sites-portal/be-listeners.png)

## <a name="create-routing-rules"></a>创建路由规则

规则按其列出的顺序进行处理，并且流量使用匹配的第一个规则进行定向，而无论特殊性如何。 例如，如果在同一端口上同时有使用基本侦听器的规则和使用多站点侦听器的规则，则使用多站点侦听器的规则必须在使用基本侦听器的规则之前列出，多站点规则才能正常运行。 

在此示例中，将创建两个新规则并删除在创建应用程序网关时创建的默认规则。

1. 依次单击“规则”、“基本”。
2. 输入 *contosoRule* 作为名称。
3. 对于侦听器，选择 *contosoListener*。
4. 对于后端池，选择 *contosoPool*。

    ![创建基于路径的规则](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. 单击 **“确定”** 。
6. 使用名称 *fabrikamRule*、*fabrikamListener* 和 *fabrikamPool* 创建第二个规则。
7. 通过单击名为 *rule1* 的默认规则并单击“删除”删除该规则。

## <a name="create-a-cname-record-in-your-domain"></a>在域中创建 CNAME 记录

使用其公共 IP 地址创建应用程序网关后，可以获取 DNS 地址并使用它在域中创建 CNAME 记录。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

1. 单击“所有资源”，然后单击“myAGPublicIPAddress”。

    ![记下应用程序网关的 DNS 地址](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. 复制 DNS 地址，并将其用作域中新 CNAME 记录的值。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在浏览器的地址栏中输入域名。 例如， http://www.contoso.com。

    ![在应用程序网关中测试 contoso 站点](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. 将地址更改为其他域，应看到类似下例所示的内容：

    ![在应用程序网关中测试 fabrikam 站点](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户的左侧菜单上选择“资源组”。
2. 在“资源组”页的列表中搜索“myResourceGroupAG”，然后将其选中。
3. 在“资源组”页上，选择“删除资源组”。
4. 在“键入资源组名称”字段中输入“myResourceGroupAG”，然后选择“删除”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关如何使用 Azure 应用程序网关的详细信息](application-gateway-introduction.md)

<!-- Update_Description: wording update -->