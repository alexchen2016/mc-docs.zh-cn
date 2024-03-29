---
title: 将 HDInsight 连接到本地网络 - Azure HDInsight | Azure
description: 了解如何在 Azure 虚拟网络中创建 HDInsight 群集，然后将其连接到本地网络。 了解如何使用自定义 DNS 服务器在 HDInsight 和本地网络之间配置名称解析。
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 12/28/2018
ms.date: 02/04/2019
ms.author: v-yiso
ms.openlocfilehash: 63b559a15b5792ebc0711bd0b35c8b18a1c12157
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625422"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>将 HDInsight 连接到本地网络

了解如何使用 Azure 虚拟网络和 VPN 网关将 HDInsight 连接到本地网络。 本文档提供以下相关规划信息：

* 在连接到本地网络的 Azure 虚拟网络上使用 HDInsight。

* 配置虚拟网络与本地网络之间的 DNS 名称解析。

* 将网络安全组配置为限制对 HDInsight 的 Internet 访问。

* 虚拟网络上由 HDInsight 提供的端口。

## <a name="create-the-virtual-network-configuration"></a>创建虚拟网络配置

请参阅以下文档，了解如何创建连接到本地网络的 Azure 虚拟网络：

* [使用 Azure 门户](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [使用 Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [使用 Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>配置名称解析

若要让 HDInsight 和已连接网络中的资源通过名称通信，必须执行以下操作：

* 在 Azure 虚拟网络中创建自定义 DNS 服务器。

* 将虚拟网络配置为使用自定义 DNS 服务器而非默认的 Azure 递归解析程序。

* 配置自定义 DNS 服务器和本地 DNS 服务器之间的转发。

该配置启用以下行为：

* 将完全限定的域名（其中包含虚拟网络的 DNS 后缀）的请求转发到自定义 DNS 服务器。 自定义 DNS 服务器然后会将这些请求转发到 Azure 递归解析程序，由后者返回 IP 地址。

* 将所有其他请求转发到本地 DNS 服务器。 甚至对公共 Internet 资源（如 microsoft.com）的请求也将因名称解析转发至本地 DNS 服务器。

在下面的关系图中，绿线表示以虚拟网络的 DNS 后缀结尾的资源请求。 蓝线表示本地网络或公共 Internet 上的资源请求。

![本文档将使用如何解析配置中的 DNS 请求的关系图](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>创建自定义 DNS 服务器

> [!IMPORTANT]
> 必须先创建并配置 DNS 服务器，此后再将 HDInsight 安装到虚拟网络中。

这些步骤通过 [Azure 门户](https://portal.azure.cn)创建 Azure 虚拟机。 关于其他创建虚拟机的方法，请参阅[创建 VM - Azure CLI](../virtual-machines/linux/quick-create-cli.md) 和[创建 VM - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)。  若要创建使用 [Bind](https://www.isc.org/downloads/bind/) DNS 软件的 Linux VM，请执行以下步骤：

  
1. 登录到 [Azure 门户](https://portal.azure.com)。
  
1. 在左侧菜单中，选择“+ 创建资源”。
 
1. 选择“虚拟机”。

1. 选择“Ubuntu Server 18.04 LTS”。<br />  

    ![创建 Ubuntu 虚拟机](./media/connect-on-premises-network/create-ubuntu-vm.png)

1. 在“基本信息”选项卡中输入以下信息：  
  
    | 字段 | 值 |
    | --- | --- |
    |订阅 |选择相应的订阅。|
    |资源组 |选择包含此前创建的虚拟网络的资源组。|
    |虚拟机名称 | 输入用于标识该虚拟机的友好名称。 本示例使用 **DNSProxy**。|
    |区域 | 选择与此前创建的虚拟网络相同的区域。  并非所有 VM 大小都可在所有区域中使用。  |
    |可用性选项 |  选择所需的可用性级别。  Azure 提供一系列的选项，用于管理应用程序的可用性和复原能力。  将解决方案构建为使用可用性区域或可用性集中的已复制 VM，使应用和数据免受事件中心中断和维护事件的影响。 此示例使用“不需要基础结构冗余”。 |
    |映像 | 为 VM 选择基础操作系统或应用程序。  对于此示例，请选择最小和最低成本选项。 |
    |身份验证类型 | __密码__或 __SSH 公钥__：SSH 帐户的身份验证方法。 建议使用公钥，因为更安全。 此示例使用公钥。  有关详细信息，请参阅[为 Linux VM 创建和使用 SSH 密钥](../virtual-machines/linux/mac-create-ssh-keys.md)文档。|
    |用户名 |输入 VM 的管理员用户名。  本示例使用 **sshuser**。|
    |密码或 SSH 公钥 | 可用字段取决于针对“身份验证类型”所做的选择。  输入相应的值。|
    |||

    ![虚拟机基本配置](./media/connect-on-premises-network/vm-basics.png)

    将其他项保留为默认值，然后选择“网络”选项卡。

1. 在“网络”选项卡中，输入以下信息： 

    | 字段 | 值 |
    | --- | --- |
    |虚拟网络 | 选择此前创建的虚拟网络。|
    |子网 | 选择前面创建的虚拟网络的默认子网。 请勿选择 VPN 网关使用的子网。|
    |公共 IP | 使用自动填充的值。  |

    ![虚拟网络设置](./media/connect-on-premises-network/virtual-network-settings.png)

    将其他项保留为默认值，然后选择“查看 + 创建”。

1. 在“查看 + 创建”选项卡中，选择“创建”以创建虚拟机。
 

### <a name="review-ip-addresses"></a>查看 IP 地址
创建虚拟机后，会收到“部署成功”的通知，该通知附带一个“转到资源”按钮。  选择“转到资源”，转到新的虚拟机。  在新虚拟机的默认视图中，按照以下步骤确定关联的 IP 地址：

1. 在“设置”中，选择“属性”。 

1. 记下“公共 IP 地址/DNS 名称标签”和“专用 IP 地址”的值供以后使用。

    ![公共和专用 IP 地址](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>安装和配置 Bind（DNS 软件）

1. 使用 SSH 连接到虚拟机的公共 IP 地址。 以下示例将在 40.68.254.142 连接到虚拟机：

    ```bash
    ssh sshuser@40.68.254.142
    ```

    将 `sshuser` 替换为创建群集时指定的 SSH 用户帐户。

   > [!NOTE]
   > 可通过多种方法获取 `ssh` 实用工具。 在 Linux、Unix 和 macOS 上，操作系统会附带此实用工具。 如果使用的是 Windows，请考虑以下选项之一：
   > 
   > * [Windows 10 版 Bash on Ubuntu](https://msdn.microsoft.com/commandline/wsl/about)
   > * [Git (https://git-scm.com/)](https://git-scm.com/)
   > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. 若要安装 Bind，请通过 SSH 会话使用以下命令：

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 若要配置 Bind，以便将名称解析请求转发到本地 DNS 服务器，请使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > 将 `goodclients` 节中的值替换为虚拟网络和本地网络的 IP 地址范围。 此节定义此 DNS 服务器从其接受请求的地址。
    >
    > 将 `forwarders` 节中的 `192.168.0.1` 项替换为本地 DNS 服务器的 IP 地址。 此项将 DNS 请求路由到本地 DNS 服务器进行解析。

    若要编辑该文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    若要保存文件，请使用 Ctrl+X、Y，然后按 Enter。

4. 在 SSH 会话中使用以下命令：

    ```bash
    hostname -f
    ```

    此命令返回类似于以下文本的值：

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn` 文本是此虚拟网络的 DNS 后缀。 保存该值，因为以后会用到。

5. 若要配置 Bind，以便为虚拟网络中的资源解析 DNS 名称，请使用以下文本作为 `/etc/bind/named.conf.local` 文件的内容：

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > 必须将 `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn` 替换为此前检索的 DNS 后缀。

    若要编辑该文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    若要保存文件，请使用 Ctrl+X、Y，然后按 Enter。

6. 若要启动 Bind，请使用以下命令：

    ```bash
    sudo service bind9 restart
    ```

7. 若要验证 Bind 能否解析本地网络中资源的名称，请使用以下命令：

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > 将 `dns.mynetwork.net` 替换为本地网络中资源的完全限定的域名 (FQDN)。
    >
    > 将 `10.0.0.4` 替换为虚拟网络中自定义 DNS 服务器的内部 IP 地址。

    显示的响应类似于以下文本：

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>将虚拟网络配置为使用自定义 DNS 服务器

若要配置虚拟网络以使用自定义 DNS 服务器，而不是 Azure 递归解析程序，请在 [Azure 门户](https://portal.azure.cn)中使用以下步骤：

1. 在左侧菜单中，选择“所有服务”。  

1. 在“网络”下，选择“虚拟网络”。  

1. 从列表中选择虚拟网络，此时会打开虚拟网络的默认视图。  

1. 在默认视图中的“设置”下，选择“DNS 服务器”。  

1. 选择“自定义”，然后输入自定义 DNS 服务器的专用 IP 地址。   

1. 选择“其他安全性验证” 。  <br />  

    ![设置网络的自定义 DNS 服务器](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>配置本地 DNS 服务器

前一部分已将自定义 DNS 服务器配置为将请求转发到本地 DNS 服务器。 接下来，必须将本地 DNS 服务器配置为将请求转发到自定义 DNS 服务器。

有关 DNS 服务器配置的具体步骤，请参阅 DNS 服务器软件的文档。 请查找配置条件转发器的步骤。

条件转发仅转发对特定 DNS 后缀的请求。 在此示例中，必须为虚拟网络的 DNS 后缀配置转发器。 对此后缀的请求应转发到自定义 DNS 服务器的 IP 地址。 

以下文本是 Bind DNS 软件的条件转发器配置的示例：

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

若要了解如何在 Windows Server 2016 上使用 DNS，请参阅 [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) 文档。

配置本地 DNS 服务器以后，即可在本地网络中使用 `nslookup` 来验证能否解析虚拟网络中的名称。 下面为示例 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.chinacloudapp.cn 196.168.0.4
```

该示例使用位于 196.168.0.4 的本地 DNS 服务器来解析自定义 DNS 服务器的名称。 将该 IP 地址替换为本地 DNS 服务器的 IP 地址。 将 `dnsproxy` 地址替换为自定义 DNS 服务器的完全限定的域名。

## <a name="optional-control-network-traffic"></a>可选：控制网络流量

可以使用网络安全组 (NSG) 或用户定义路由 (UDR) 来控制网络通信。 NSG 用于筛选入站和出站流量，以及允许或拒绝流量。 UDR 用于控制虚拟网络、Internet 和本地网络中各资源之间的流量。

> [!WARNING]
> HDInsight 要求从 Azure 云中的特定 IP 地址进行入站访问，以及进行不受限制的出站访问。 使用 NSG 或 UDR 控制流量时，必须执行以下步骤：

1. 找到虚拟网络所在位置的 IP 地址。 如需按位置列出的必需 IP，请参阅[必需 IP 地址](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)。

2. 对于步骤 1 中确定的 IP 地址，允许该 IP 地址的入站流量。

   * 如果使用 __NSG__：在端口 443上允许该 IP地址的入站流量。
   * 如果使用 __UDR__：为该 IP 地址将路由的下一个跃点类型设置为“Internet”。

如需使用 Azure PowerShell 或 Azure CLI 来创建 NSG 的示例，请参阅[使用 Azure 虚拟网络扩展 HDInsight](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) 文档。

## <a name="create-the-hdinsight-cluster"></a>创建 HDInsight 群集

> [!WARNING]
> 必须先配置自定义 DNS 服务器，然后才能将 HDInsight 安装到虚拟网络中。

通过[使用 Azure 门户创建 HDInsight 群集](./hdinsight-hadoop-create-linux-clusters-portal.md)文档中的步骤创建 HDInsight 群集。

> [!WARNING]
> * 在群集创建过程中，必须选择虚拟网络所在的位置。
>
> * 在配置的“高级设置”部分，必须选择此前创建的虚拟网络和子网。

## <a name="connecting-to-hdinsight"></a>连接到 HDInsight

HDInsight 上的大多数文档假定你可以通过 Internet 访问群集。 例如，可以通过 https://CLUSTERNAME.azurehdinsight.cn 连接到该群集。 此地址使用公共网关，如果你使用了 NSG 或 UDR 限制来自 Internet 的访问，则该网关不可用。
一些文档在通过 SSH 会话连接到群集时还引用了 `headnodehost`。 该地址仅可在群集中的节点上使用，在通过虚拟网络连接的客户端上不可用。

若要通过虚拟网络直接连接到 HDInsight，请使用以下步骤：

1. 若要发现 HDInsight 群集节点的内部完全限定的域名，请使用以下方法之一：

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. 若要确定服务的可用端口，请参阅 [HDInsight 的 Apache Hadoop 服务所用的端口](./hdinsight-hadoop-port-settings-for-services.md)文档。

    > [!IMPORTANT]
    > 托管在头节点上的某些服务一次只能在一个节点上处于活动状态。 如果尝试在一个头节点上访问某个服务时失败，请切换到其他头节点。
    >
    > 例如，Apache Ambari 一次仅在一个头节点上处于活动状态。 如果在一个头节点上尝试访问 Ambari 并返回 404 错误，则它正在其他头节点上运行。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何在虚拟网络中使用 HDInsight，请参阅[使用 Azure 虚拟网络扩展 HDInsight](./hdinsight-extend-hadoop-virtual-network.md)。

* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。

* 有关用户定义路由的详细信息，请参阅[用户定义路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
<!--Update_Description: wording update-->