---
title: 从 CLI 重置 Linux VM 密码和 SSH 密钥 | Azure
description: 如何使用 VMAccess 扩展从 Azure 命令行接口 (CLI) 重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
origin.date: 04/30/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.openlocfilehash: b085e81ab2952b0f7f7f28b85bbfa352e5a06b92
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627290"
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>如何使用 VMAccess 扩展重置 Linux VM 密码或 SSH 密钥、修复 SSH 配置，以及检查磁盘一致性
如果因为忘记密码、安全外壳 (SSH) 密钥不正确或 SSH 配置出现问题而不能连接到 Azure 上的 Linux 虚拟机，请使用 VMAccessForLinux 扩展通过 Azure CLI 重置密码或 SSH 密钥、修复 SSH 配置以及检查磁盘一致性。 

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Azure 建议大多数新部署使用 Resource Manager 模型。 了解如何[使用 Resource Manager 模型执行这些步骤](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。

借助 Azure CLI，可以从命令行接口（Bash、终端、命令提示符）使用 **azure vm extension set** 命令来访问各种命令。 运行 **azure help vm extension set** 可了解扩展的详细用法。

借助 Azure CLI，可以执行以下任务：

* [重置密码](#pwresetcli)
* [重置 SSH 密钥](#sshkeyresetcli)
* [重置密码和 SSH 密钥](#resetbothcli)
* [创建新的 sudo 用户帐户](#createnewsudocli)
* [重置 SSH 配置](#sshconfigresetcli)
* [删除用户](#deletecli)
* [显示 VMAccess 扩展状态](#statuscli)
* [检查添加磁盘的一致性](#checkdisk)
* [修复在 Linux VM 中添加的磁盘](#repairdisk)

## <a name="prerequisites"></a>先决条件
需要执行以下操作：

* 需要[安装 Azure CLI](../../../cli-install-nodejs.md) 并[连接到订阅](https://docs.azure.cn/zh-cn/cli/authenticate-azure-cli?view=azure-cli-latest)才能使用与帐户关联的 Azure 资源。
* 在命令提示符下键入以下命令，为经典部署模型设置正确的模式：
    ``` 
        azure config mode asm
    ```
* 设置一个新密码或一组新 SSH 密钥（如果想要重置任一项）。 如果想要重置 SSH 配置，则不需要这些。

<a name="pwresetcli"></a>
## <a name="reset-the-password"></a>重置密码
1. 使用以下代码行在本地计算机上创建名为 PrivateConf.json 的文件。 将 **myUserName** 和 <strong>myP@ssW0rd</strong> 替换为自己的用户名和密码，并设置自己的到期日期。

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```

2. 运行以下命令（请将 **myVM**替换为自己的虚拟机名称）。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

<a name="sshkeyresetcli"></a>
## <a name="reset-the-ssh-key"></a>重置 SSH 密钥
1. 使用以下内容创建名为 PrivateConf.json 的文件。 将 **myUserName** 和 **mySSHKey** 值替换为自己的信息。

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. 运行以下命令（请将 **myVM**替换为自己的虚拟机名称）。

        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

<a name="resetbothcli"></a>
## <a name="reset-both-the-password-and-the-ssh-key"></a>重置密码和 SSH 密钥
1. 使用以下内容创建名为 PrivateConf.json 的文件。 将 **myUserName**、**mySSHKey** 和 <strong>myP@ssW0rd</strong> 值替换为自己的信息。

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. 运行以下命令（请将 **myVM**替换为自己的虚拟机名称）。

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

<a name="createnewsudocli"></a>
## <a name="create-a-new-sudo-user-account"></a>创建新的 sudo 用户帐户

如果忘记用户名，可以使用 VMAccess 创建具有 sudo 权限的新用户帐户。 在这种情况下，不会修改现有的用户名和密码。

若要创建具有密码访问权限的新 sudo 用户，请使用 [重置密码](#pwresetcli) 中的脚本并指定新用户名。

若要创建具有 SSH 密钥访问权限的新 sudo 用户，请使用[“重置 SSH 密钥”](#sshkeyresetcli) 中的脚本并指定新用户名。

还可以使用[“重置密码和 SSH 密钥”](#resetbothcli)创建同时具有密码和 SSH 密钥访问权限的新用户。

<a name="sshconfigresetcli"></a>
## <a name="reset-the-ssh-configuration"></a>重置 SSH 配置
如果 SSH 配置处于某种意外状态，可能会丢失对 VM 的访问权限。 可以使用 VMAccess 扩展将配置重置为其默认状态。 为此，只需将“reset_ssh”键设置为“True”。 该扩展将重新启动 SSH 服务器，打开 VM 上的 SSH 端口，然后将 SSH 配置重置为默认值。 将不会更改用户帐户（名称、密码或 SSH 密钥）。

> [!NOTE]
> 重置后的 SSH 配置文件位于 /etc/ssh/sshd_config 中。
> 
> 

1. 使用以下内容创建名为 PrivateConf.json 的文件。

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. 运行以下命令（请将 **myVM**替换为自己的虚拟机名称）。 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

<a name="deletecli"></a>
## <a name="delete-a-user"></a>删除用户
如果想要不登录 VM 就直接删除用户帐户，可以使用此脚本。

1. 创建包含以下内容的名为 PrivateConf.json 的文件（请将 **removeUserName**替换为要删除的用户名）。 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. 运行以下命令（请将 **myVM**替换为自己的虚拟机名称）。 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

<a name="statuscli"></a>
## <a name="display-the-status-of-the-vmaccess-extension"></a>显示 VMAccess 扩展的状态
若要显示 VMAccess 扩展状态，请运行以下命令。

```
        azure vm extension get
```

## <a name='checkdisk'></a>检查添加磁盘的一致性
若要在 Linux 虚拟机的所有磁盘上运行 fsck，需执行以下操作：

1. 使用以下内容创建名为 PublicConf.json 的文件。 Check Disk 采用的布尔值表示是否检查附加到虚拟机的磁盘。 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. 执行以下命令（请将 **myVM** 替换为自己的虚拟机名称）。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>修复磁盘
若要修复无法装入或存在装入配置错误的磁盘，请使用 VMAccess 扩展重置 Linux 虚拟机上的装入配置。 请将 **myDisk** 替换为自己的磁盘名称。

1. 使用以下内容创建名为 PublicConf.json 的文件。 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. 执行以下命令（请将 **myVM** 替换为自己的虚拟机名称）。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>后续步骤
* 若要使用 Azure PowerShell cmdlet 或 Azure 资源管理器模板来重置密码或 SSH 密钥、修复 SSH 配置和检查磁盘一致性，请参阅 [GitHub 上的 VMAccess 扩展文档](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。 
* 也可以使用 [Azure 门户](https://portal.azure.cn)来重置部署在经典部署模型中的 Linux VM 的密码或 SSH 密钥。 目前你无法使用门户来针对通过 Resource Manager 部署模型部署的 Linux VM 执行上述操作。
* 有关使用适用于 Azure 虚拟机的 VM 扩展的详细信息，请参阅[关于虚拟机扩展和功能](../extensions-features.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)。
<!-- Update_Description: update meta properties -->

