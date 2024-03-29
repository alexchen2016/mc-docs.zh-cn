---
title: 如何在 Azure 上安装 MySQL | Azure
description: 了解如何在 Azure 中的 Linux 虚拟机（Ubuntu 或 CentOS OS）上安装 MySQL 堆栈
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
origin.date: 02/01/2016
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: f2c00d0f8c3165c78a74a9e3e24247e9e9ef1c4a
ms.sourcegitcommit: 3b05a8982213653ee498806dc9d0eb8be7e70562
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59003690"
---
<!-- Remove the Red Hat family in meta properties-->
# <a name="how-to-install-mysql-on-azure"></a>如何在 Azure 上安装 MySQL
本文介绍如何在运行 Linux 的 Azure 虚拟机上安装和配置 MySQL。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>在虚拟机上安装 MySQL
> [!NOTE]
> 必须已经有一个运行 Linux 的 Azure 虚拟机，才能完成本教程。 继续操作前，请参阅 [Azure Linux VM 教程](quick-create-cli.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)创建并设置一个 Linux VM，其中 `mysqlnode` 为 VM 名称，`azureuser` 为用户。
> 
> 

在此情况下，请使用 3306 端口作为 MySQL 端口。  

通过 putty 连接到创建的 Linux VM。 首次使用 Azure Linux VM，请参阅[此处](mac-create-ssh-keys.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)了解如何使用 putty 连接到 Linux VM。

我们将使用存储库包来安装 MySQL5.6，作为本文中的示例。 实际上，MySQL5.6 在性能上相对于 MySQL5.5 而言有更大的改进。  单击[此处](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)查看详细信息。

### <a name="how-to-install-mysql56-on-ubuntu"></a>如何在 Ubuntu 上安装 MySQL5.6
此处将使用 Azure 中的 Linux VM（Ubuntu）。

* 步骤 1：安装 MySQL Server 5.6（切换到 `root` 用户）：

            #[azureuser@mysqlnode:~]sudo su -

    安装 mysql-server 5.6：

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    在安装过程中，会看到如下所示的对话窗口显示，要求设置 MySQL 根密码。需要在此处设置该密码。

    ![图像](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    再次输入密码进行确认。

    ![图像](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* 步骤 2：登录 MySQL Server

    MySQL Server 安装完成后，将自动启动 MySQL 服务。 可以使用 `root` 用户登录 MySQL Server。
    使用以下命令登录并输入密码。

             #[root@mysqlnode ~]# mysql -uroot -p
* 步骤 3：管理正在运行的 MySQL 服务

    (a) 获取 MySQL 服务的状态

             #[root@mysqlnode ~]# service mysql status

    (b) 启动 MySQL 服务

             #[root@mysqlnode ~]# service mysql start

    (c) 停止 MySQL 服务

             #[root@mysqlnode ~]# service mysql stop

    (d) 重启 MySQL 服务

             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-centos"></a>如何在 CentOS 上安装 MySQL

<!-- Not Avaiable on Red Hat OS 系列（例如 CentOS、Oracle Linux） -->

此处会将 CentOS 与 Linux VM 一起使用。

* 步骤 1：添加 MySQL Yum 存储库（切换到 `root` 用户）：

            #[azureuser@mysqlnode:~]sudo su -

    下载并安装 MySQL 发行包：

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* 步骤 2：编辑以下文件，以便允许 MySQL 存储库下载 MySQL5.6 程序包。

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    将此文件的每个值更新为下面的值：

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* 步骤 3：从 MySQL 存储库的“安装 MySQL”安装 MySQL：

           #[root@mysqlnode ~]#yum install mysql-community-server

    将安装 MySQL RPM 程序包和所有相关的程序包。
* 步骤 4：管理正在运行的 MySQL 服务

    (a) 检查 MySQL Server 的服务状态：

           #[root@mysqlnode ~]#service mysqld status

    (b) 检查 MySQL Server 的默认端口是否正在运行：

           #[root@mysqlnode ~]#netstat  -tunlp|grep 3306

    (c) 启动 MySQL Server：

           #[root@mysqlnode ~]#service mysqld start

    (d) 停止 MySQL Server：

           #[root@mysqlnode ~]#service mysqld stop

    (e) 将 MySQL 设置为在系统启动时启动：

           #[root@mysqlnode ~]#chkconfig mysqld on

### <a name="how-to-install-mysql-on-suse-linux"></a>如何在 SUSE Linux 上安装 MySQL
此处会将 OpenSUSE 与 Linux VM 一起使用。

* 步骤 1：下载并安装 MySQL Server

    通过以下命令切换到 `root` 用户：  

           #sudo su -

    下载并安装 MySQL 程序包：

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* 步骤 2：管理正在运行的 MySQL 服务

    (a) 检查 MySQL Server 的状态：

           #[root@mysqlnode ~]# rcmysql status

    (b) 检查 MySQL Server 的默认端口是否正在运行：

           #[root@mysqlnode ~]# netstat  -tunlp|grep 3306

    (c) 启动 MySQL Server：

           #[root@mysqlnode ~]# rcmysql start

    (d) 停止 MySQL Server：

           #[root@mysqlnode ~]# rcmysql stop

    (e) 将 MySQL 设置为在系统启动时启动：

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>后续步骤
在[此处](https://www.mysql.com/)查找更多有关 MySQL 的使用方法和信息。

<!-- Update_Description: update meta properties  -->