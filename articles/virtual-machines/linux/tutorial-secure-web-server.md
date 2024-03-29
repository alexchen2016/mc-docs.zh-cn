---
title: 教程：在 Azure 中使用 Key Vault 中存储的 SSL 证书保护 Linux 虚拟机上的 Web 服务器 | Azure
description: 本教程介绍如何通过 Azure CLI 使用 Azure Key Vault 中存储的 SSL 证书来保护运行 NGINX Web 服务器的 Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 04/30/2018
ms.date: 05/20/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 4f1447f4d41d7a5fc35c10dd6c4b7a9ee60f9702
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004251"
---
# <a name="tutorial-secure-a-web-server-on-a-linux-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>教程：在 Azure 中使用 Key Vault 中存储的 SSL 证书保护 Linux 虚拟机上的 Web 服务器
若要保护 Web 服务器，可以使用安全套接字层 (SSL) 证书来加密 Web 流量。 这些 SSL 证书可存储在 Azure Key Vault 中，并可安全部署到 Azure 中的 Linux 虚拟机 (VM)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Azure Key Vault
> * 生成证书或将其上传到 Key Vault
> * 创建 VM 并安装 NGINX Web 服务器
> * 将证书注入 VM 并使用 SSL 绑定配置 NGINX

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="overview"></a>概述
Azure Key Vault 保护加密密钥和机密，例如证书或密码。 Key Vault 有助于简化证书管理过程，让我们持续掌控用于访问这些证书的密钥。 可以在 Key Vault 中创建自签名证书，或者上传已拥有的现有受信任证书。

无需使用包含植入证书的自定义 VM 映像，而可将证书直接注入正在运行的 VM。 此过程可确保在部署过程中，在 Web 服务器上安装最新的证书。 如果续订或替换了证书，也不需要创建新的自定义 VM 映像。 创建附加的 VM 时，会自动注入最新证书。 在整个过程中，证书永远不会离开 Azure 平台，也不会在脚本、命令行历史记录或模板中公开。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault
创建 Key Vault 和证书之前，需使用 [az group create](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”  位置创建名为 *myResourceGroupSecureWeb* 的资源组：

```azurecli 
az group create --name myResourceGroupSecureWeb --location chinaeast
```

接下来，使用 [az keyvault create](https://docs.azure.cn/zh-cn/cli/keyvault?view=azure-cli-latest#az-keyvault-create) 创建 Key Vault，并在部署 VM 时启用该 Key Vault。 每个 Key Vault 均需具备唯一名称且全部小写。 将下例中的 *\<mykeyvault>* 替换为自己唯一的 Key Vault 名称：

```azurecli 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>生成证书并存储在 Key Vault 中
为供生产使用，应通过 [az keyvault certificate import](https://docs.azure.cn/zh-cn/cli/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) 导入由受信任的提供程序签名的有效证书。 在本教程中，以下示例显示了如何使用 [az keyvault certificate create](https://docs.azure.cn/zh-cn/cli/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-create) 生成使用默认证书策略的自签名证书：

```azurecli 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>准备用于 VM 的证书
若要在 VM 创建过程中使用该证书，请使用 [az keyvault secret list-versions](https://docs.azure.cn/zh-cn/cli/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list-versions) 获取证书的 ID。 使用 [az vm secret format](https://docs.azure.cn/zh-cn/cli/vm/secret?view=azure-cli-latest#az-vm-secret-format) 转换该证书。 以下示例将这些命令的输出分配给变量，以便在后续步骤中使用：

```azurecli 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>创建 cloud-init 配置以保护 NGINX
[Cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 在初始启动期间运行 cloud-init 时，无需额外的步骤和代理即可应用配置。

创建 VM 时，证书和密钥都将存储在受保护的 /var/lib/waagent/  目录中。 若要自动将证书添加到 VM 并配置 Web 服务器，请使用 cloud-init。 本示例中将安装并配置 NGINX Web 服务器。 可以使用相同的过程来安装和配置 Apache。 

创建名为 *cloud-init-web-server.txt* 的文件并粘贴以下配置：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>创建安全 VM
现在，请使用 [az vm create](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 使用 `--secrets` 参数注入 Key Vault 中的证书数据。 使用 `--custom-data` 参数传入 cloud-init 配置：

```azurecli 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

创建 VM、安装程序包和启动应用需耗时几分钟。 创建 VM 后，请记下 Azure CLI 显示的 `publicIpAddress`。 此地址用于在 Web 浏览器中访问站点。

若要使 VM 能使用安全的 Web 流量，请通过 [az vm open-port](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-open-port) 从 Internet 打开端口 443：

```azurecli 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```

### <a name="test-the-secure-web-app"></a>测试 Web 应用是否安全
现在可以打开 Web 浏览器，并在地址栏中输入 *https://\<publicIpAddress>* 。 在 VM 创建过程中提供自己的公共 IP 地址。 若使用自签名的证书，请接受安全警告：

![接受 Web 浏览器安全警告](./media/tutorial-secure-web-server/browser-warning.png)

随即显示受保护的 NGINX 站点，如下例所示：

![查看运行中的安全 NGINX 站点](./media/tutorial-secure-web-server/secured-nginx.png)

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 Azure Key Vault 中存储的 SSL 证书保护 NGINX Web 服务器。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure Key Vault
> * 生成证书或将其上传到 Key Vault
> * 创建 VM 并安装 NGINX Web 服务器
> * 将证书注入 VM 并使用 SSL 绑定配置 NGINX

请访问以下链接，查看预先生成的虚拟机脚本示例。

> [!div class="nextstepaction"]
> [Linux 虚拟机脚本示例](./cli-samples.md)

<!--Update_Description: update meta properties, wording update -->