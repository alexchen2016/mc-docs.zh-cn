---
title: Azure CLI 脚本示例 - 使用 Azure CLI 对多个网站进行负载均衡
description: Azure CLI 脚本示例 - 对指向同一虚拟机的多个网站进行负载均衡
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
origin.date: 07/07/2017
ms.date: 01/07/2019
ms.author: v-biyu
ms.openlocfilehash: d863696b0b12aaf5688a616c42953878eee6e3d1
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627643"
---
# <a name="load-balance-multiple-websites"></a>对多个网站进行负载均衡

此脚本示例会使用可用性集中的两台虚拟机 (VM) 创建虚拟网络。 负载均衡器会将两个单独 IP 地址的流量定向到两台 VM。 运行脚本后，可将 Web 服务器软件部署到 VM 上，并可承载多个网站，其中每个网站都有其自身的 IP 地址。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```bash
# !/bin/bash

RgName="MyResourceGroup"
Location="chinaeast"

# Create a resource group.
az group create \
  --name $RgName \
  --location $Location

# Create an availability set for the two VMs that host both websites.
az vm availability-set create \
  --resource-group $RgName \
  --location $Location \
  --name MyAvailabilitySet \
  --platform-fault-domain-count 2 \
  --platform-update-domain-count 2

# Create a virtual network and a subnet.
az network vnet create \
  --resource-group $RgName \
  --name MyVnet \
  --address-prefix 10.0.0.0/16 \
  --location $Location \
  --subnet-name MySubnet \
  --subnet-prefix 10.0.0.0/24

# Create three public IP addresses; one for the load balancer and two for the front-end IP configurations.
az network public-ip create \
  --resource-group $RgName \
  --name MyPublicIp-LoadBalancer \
  --allocation-method Dynamic
az network public-ip create \
  --resource-group $RgName \
  --name MyPublicIp-Contoso \
  --allocation-method Dynamic
az network public-ip create \
  --resource-group $RgName \
  --name MyPublicIp-Fabrikam \
  --allocation-method Dynamic

# Create a load balancer.
az network lb create \
  --resource-group $RgName \
  --location $Location \
  --name MyLoadBalancer \
  --frontend-ip-name FrontEnd \
  --backend-pool-name BackEnd \
  --public-ip-address MyPublicIp-LoadBalancer

# Create two front-end IP configurations for both web sites.
az network lb frontend-ip create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --public-ip-address MyPublicIp-Contoso \
  --name FeContoso
az network lb frontend-ip create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --public-ip-address MyPublicIp-Fabrikam \
  --name FeFabrikam

# Create the back-end address pools.
az network lb address-pool create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --name BeContoso
az network lb address-pool create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --name BeFabrikam

# Create a probe on port 80.
az network lb probe create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --name MyProbe \
  --protocol Http \
  --port 80 --path /

# Create the load balancing rules.
az network lb rule create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --name LBRuleContoso \
  --protocol Tcp \
  --probe-name MyProbe \
  --frontend-port 5000 \
  --backend-port 5000 \
  --frontend-ip-name FeContoso \
  --backend-pool-name BeContoso
az network lb rule create \
  --resource-group $RgName \
  --lb-name MyLoadBalancer \
  --name LBRuleFabrikam \
  --protocol Tcp \
  --probe-name MyProbe \
  --frontend-port 5000 \
  --backend-port 5000 \
  --frontend-ip-name FeFabrikam \
  --backend-pool-name BeFabrikam

# ############## VM1 ###############

# Create an Public IP for the first VM.
az network public-ip create \
  --resource-group $RgName \
  --name MyPublicIp-Vm1 \
  --allocation-method Dynamic

# Create a network interface for VM1.
az network nic create \
  --resource-group $RgName \
  --vnet-name MyVnet \
  --subnet MySubnet \
  --name MyNic-Vm1 \
  --public-ip-address MyPublicIp-Vm1

# Create IP configurations for Contoso and Fabrikam.
az network nic ip-config create \
  --resource-group $RgName \
  --name ipconfig2 \
  --nic-name MyNic-Vm1 \
  --lb-name MyLoadBalancer \
  --lb-address-pools BeContoso
az network nic ip-config create \
  --resource-group $RgName \
  --name ipconfig3 \
  --nic-name MyNic-Vm1 \
  --lb-name MyLoadBalancer \
  --lb-address-pools BeFabrikam

# Create Vm1.
az vm create \
  --resource-group $RgName \
  --name MyVm1 \
  --nics MyNic-Vm1 \
  --image UbuntuLTS \
  --availability-set MyAvailabilitySet \
  --admin-username azureadmin \
  --generate-ssh-keys

###### ######### VM2 ###############

# Create an Public IP for the second VM.
az network public-ip create \
  --resource-group $RgName \
  --name MyPublicIp-Vm2 \
  --allocation-method Dynamic

# Create a network interface for VM2.
az network nic create \
  --resource-group $RgName \
  --vnet-name MyVnet \
  --subnet MySubnet \
  --name MyNic-Vm2 \
  --public-ip-address MyPublicIp-Vm2

# Create IP-Configs for Contoso and Fabrikam.
az network nic ip-config create \
  --resource-group $RgName \
  --name ipconfig2 \
  --nic-name MyNic-Vm2 \
  --lb-name MyLoadBalancer \
  --lb-address-pools BeContoso
az network nic ip-config create \
  --resource-group $RgName \
  --name ipconfig3 \
  --nic-name MyNic-Vm2 \
  --lb-name MyLoadBalancer \
  --lb-address-pools BeFabrikam

# Create Vm2.
az vm create \
  --resource-group $RgName \
  --name MyVm2 \
  --nics MyNic-Vm2 \
  --image UbuntuLTS \
  --availability-set MyAvailabilitySet \
  --admin-username azureadmin \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟网络、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/zh-cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.azure.cn/zh-cn/cli/network/vnet#az_network_vnet_create) | 创建 Azure 虚拟网络和子网。 |
| [az network public-ip create](https://docs.azure.cn/zh-cn/cli/network/public-ip#az_network_public_ip_create) | 使用静态 IP 地址和关联的 DNS 名称创建公共 IP 地址。 |
| [az network lb create](https://docs.azure.cn/zh-cn/cli/network/lb#az_network_lb_create) | 创建 Azure 负载均衡器。 |
| [az network lb probe create](https://docs.azure.cn/zh-cn/cli/network/lb/probe#az_network_lb_probe_create) | 创建负载均衡器探测。 负载均衡器探测用于监视负载均衡器集中的每个 VM。 如果任何 VM 无法访问，流量不会路由到该 VM。 |
| [az network lb rule create](https://docs.azure.cn/zh-cn/cli/network/lb/rule#az_network_lb_rule_create) | 创建负载均衡器规则。 在此示例中，为端口 80 创建一个规则。 当 HTTP 流量到达负载均衡器时，它会路由到负载均衡器集中某个 VM 的端口 80。 |
| [az network lb frontend-ip create](https://docs.azure.cn/zh-cn/cli/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | 为负载均衡器创建前端 IP 地址。 |
| [az network lb address-pool create](https://docs.azure.cn/zh-cn/cli/network/lb/address-pool#az_network_lb_address_pool_create) | 创建后端地址池。 |
| [az network nic create](https://docs.azure.cn/zh-cn/cli/network/nic#az_network_nic_create) | 创建虚拟网卡并将其连接到虚拟网络和子网。 |
| [az vm availability-set create](https://docs.azure.cn/zh-cn/cli/network/lb/rule#az_network_lb_rule_create) | 创建可用性集。 可用性集通过将虚拟机分布到各个物理资源上（以便发生故障时，不会影响整个集）来确保应用程序运行时间。 |
| [az network nic ip-config create](https://docs.azure.cn/zh-cn/cli/network/nic/ip-config#az_network_nic_ip_config_create) | 创建 IP 配置。 必须为订阅启用 Microsoft.Network/AllowMultipleIpConfigurationsPerNic 功能。 对于每个 NIC，仅能使用 --make-primary 标志将一个配置指定为主要 IP 配置。 |
| [az vm create](https://docs.azure.cn/zh-cn/cli/vm/availability-set#az_vm_availability_set_create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az group delete](https://docs.azure.cn/zh-cn/cli/vm/extension#az_vm_extension_set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli)。

可在 [Azure 网络概述文档](../cli-samples.md?toc=%2fnetworking%2ftoc.json)中找到其他网络 CLI 脚本示例。
