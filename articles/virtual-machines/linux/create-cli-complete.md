---
title: 使用 Azure CLI 创建 Linux 环境 | Azure
description: 使用 Azure CLI 从头开始创建存储、Linux VM、虚拟网络和子网、负载均衡器、NIC、公共 IP 和网络安全组。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 12/14/2017
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: bc5f6bd60dfe0e7501aba7730608de5fdee25b1c
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626159"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 创建完整的 Linux 虚拟机
若要在 Azure 中快速创建虚拟机 (VM)，可使用单个使用默认值的 Azure CLI 命令创建任何所需的支持资源。 虚拟网络、公共 IP 地址和网络安全组规则等资源均会自动创建。 为了在生产使用中更好地控制环境，可提前创建这些资源，并将 VM 添加到其中。 本文逐步介绍如何创建 VM 和每个支持资源。

确保已安装了最新的 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-latest) 并已使用 [az login](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

在以下示例中，请将示例参数名称替换成自己的值。 示例参数名称包括 *myResourceGroup*、*myVnet* 和 *myVM*。

## <a name="create-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 创建虚拟机和支持的虚拟网络资源前，必须先创建资源组。 使用 [az group create](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”位置创建名为“myResourceGroup”的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

默认情况下，Azure CLI 命令的输出采用 JSON（JavaScript 对象表示法）格式。 若要更改列表或表的默认输出，请使用 [az configure --output](https://docs.azure.cn/zh-cn/cli/reference-index?view=azure-cli-latest#az-configure)。 还可以向任何命令添加 `--output` 以一次性地更改输出格式。 以下示例显示了来自 `az group create` 命令的 JSON 输出：

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "chinaeast",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网
接下来，在 Azure 中创建一个虚拟网络，以及一个可在其中创建 VM 的子网。 使用 [az network vnet create](https://docs.azure.cn/zh-cn/cli/network/vnet?view=azure-cli-latest#az-network-vnet-create) 创建一个名为 *myVnet* 的虚拟网络，其地址前缀为 *192.168.0.0/16*。 还需添加一个名为 *mySubnet* 的子网，其地址前缀为 *192.168.1.0/24*：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

输出显示子网是在虚拟网络内以逻辑方式创建的：

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "chinaeast",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址
现在使用 [az network public-ip create](https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 创建公共 IP 地址。 可使用此公共 IP 地址从 Internet 连接到 VM。 因为默认地址是动态的，所以创建一个带 `--domain-name-label` 参数的命名 DNS 条目。 以下示例创建一个名为 *myPublicIP* 的公共 IP，其 DNS 名称为 *mypublicdns*。 DNS 名称必须唯一，因此请提供自己的唯一 DNS 名称：

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

输出：

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.chinaeast.cloudapp.chinacloudapi.cn",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "chinaeast",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```

<!-- cloud.azure.com to cloudapp.chinacloudapi.cn is Correct -->

## <a name="create-a-network-security-group"></a>创建网络安全组
若要控制传入和传出 VM 的流量，请将网络安全组应用到虚拟 NIC 或子网。 以下示例使用 [az network nsg create](https://docs.azure.cn/zh-cn/cli/network/nsg?view=azure-cli-latest#az-network-nsg-create) 创建一个名为 *myNetworkSecurityGroup* 的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

定义允许或拒绝特定流量的规则。 若要允许端口 22 上的入站连接（以便启用 SSH 访问），请使用 [az network nsg rule create](https://docs.azure.cn/zh-cn/cli/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 命令创建入站规则。 以下示例创建名为 *myNetworkSecurityGroupRuleSSH* 的规则：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

若要允许端口 80 上的入站连接（针对 Web 流量），请添加另一网络安全组规则。 以下示例创建名为 *myNetworkSecurityGroupRuleHTTP* 的规则：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

使用 [az network nsg show](https://docs.azure.cn/zh-cn/cli/network/nsg?view=azure-cli-latest#az-network-nsg-show) 观察网络安全组和规则：

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

输出：

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "chinaeast",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>创建虚拟 NIC
由于可将规则应用到虚拟网络接口卡 (NIC) 的使用上，因此能以编程方式使用它。 可以将多个虚拟 NIC 附加到 VM，具体取决于 [VM 大小](sizes.md)。 在以下 [az network nic create](https://docs.azure.cn/zh-cn/cli/network/nic?view=azure-cli-latest#az-network-nic-create) 命令中，会创建一个名为 *myNic* 的 NIC，并将其与网络安全组相关联。 公共 IP 地址 *myPublicIP* 也与此虚拟 NIC 相关联。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

输出：

<!-- internalDomainNameSuffix cloudapp.net to chinacloudapp.cn is correct -->

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.chinacloudapp.cn",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "chinaeast",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```

## <a name="create-an-availability-set"></a>创建可用性集
可用性集有助于将 VM 分散到容错域和更新域。 即使现在只创建一个 VM，也最好使用可用性集，以便将来更容易进行扩展。 

容错域定义共享通用电源和网络交换机的一组虚拟机。 默认情况下，在可用性集中配置的虚拟机隔离在最多三个容错域中。 其中一个容错域中的硬件问题不会影响运行应用的每个 VM。

更新域表示虚拟机组以及可同时重新启动的基础物理硬件。 在计划内维护期间，更新域的重新启动顺序可能不会按序进行，但一次只重新启动一个更新域。

将多个 VM 放入一个可用性集时，Azure 会自动将它们分散到容错域和更新域。 有关详细信息，请参阅[管理 VM 的可用性](manage-availability.md)。

使用 [az vm availability-set create](https://docs.azure.cn/zh-cn/cli/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) 为 VM 创建可用性集。 以下示例创建名为 myAvailabilitySet 的可用性集：

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

此输出表明了容错域和更新域：

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "chinaeast",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-a-vm"></a>创建 VM
现已创建用于支持可访问 Internet 的 VM 的网络资源。 现在创建 VM，并使用 SSH 密钥进行保护。 在此示例中，请根据最新的 LTS 创建 Ubuntu VM。 可使用 [az vm image list](https://docs.azure.cn/zh-cn/cli/vm/image?view=azure-cli-latest#az-vm-image-list) 查找其他映像，如[查找 Azure VM 映像](cli-ps-findimage.md)中所述。

指定用于身份验证的 SSH 密钥。 如果没有 SSH 公钥对，可[进行创建](mac-create-ssh-keys.md)或使用 `--generate-ssh-keys` 参数创建。 如果已有密钥对，此参数使用 `~/.ssh` 中现有的密钥。

使用 [az vm create](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-create) 命令并结合所有资源和信息来创建 VM。 以下示例创建一个名为 myVM 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location chinaeast \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用创建公共 IP 地址时提供的 DNS 条目通过 SSH 连接到 VM。 创建 VM 时，输出中会显示此 `fqdn`：

```json
{
  "fqdns": "mypublicdns.chinaeast.cloudapp.chinacloudapi.cn",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "chinaeast",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

<!-- fqdns cloudapp.azure.com to cloudapp.chinacloudapi.cn is Correct -->

```bash
ssh azureuser@mypublicdns.chinaeast.cloudapp.chinacloudapi.cn
```

输出：

```bash
The authenticity of host 'mypublicdns.chinaeast.cloudapp.chinacloudapi.cn (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.chinaeast.cloudapp.chinacloudapi.cn,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

可安装 NGINX 并查看到 VM 的流量。 安装 NGINX，如下所示：

```bash
sudo apt-get install -y nginx
```

若要查看活动的默认 NGINX 站点，请打开 Web 浏览器并输入 FQDN：

![VM 上的默认 NGINX 站点](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>作为模板导出
如果现在要使用相同参数创建额外的开发环境或与其匹配的生产环境，该怎么办？ Resource Manager 使用定义了所有环境参数的 JSON 模板。 通过引用此 JSON 模板构建出整个环境。 可以[手动构建 JSON 模板](../../resource-group-authoring-templates.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)，也可以通过导出现有环境来创建 JSON 模板。 使用 [az group export](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az-group-export) 导出资源组，如下所示：

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

此命令在当前工作目录中创建 `myResourceGroup.json` 文件。 从此模板创建环境时，系统会提示输入所有资源名称。 可以通过将 `--include-parameter-default-value` 参数添加到 `az group export` 命令在模板文件中填充这些名称。 请编辑 JSON 模板以指定资源名称，或[创建 parameters.json 文件](../../resource-group-authoring-templates.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)来指定资源名称。

若要从模板创建环境，请使用 [az group deployment create](https://docs.azure.cn/zh-cn/cli/group/deployment?view=azure-cli-latest#az-group-deployment-create) ，如下所示：

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

可能需要阅读[有关通过模板进行部署的详细信息](../../resource-group-template-deploy-cli.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。 了解如何对环境进行增量更新、如何使用参数文件，以及如何从单个存储位置访问模板。

## <a name="next-steps"></a>后续步骤
现在，已准备好开始使用多个网络组件和 VM。 可以使用本文介绍的核心组件，通过此示例环境构建应用程序。

<!--Update_Description: wording update, update link -->