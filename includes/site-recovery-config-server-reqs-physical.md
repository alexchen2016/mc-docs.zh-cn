---
title: include 文件
description: include 文件
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: include
origin.date: 09/03/2018
ms.date: 9/30/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 566ce9cfa20e3ded5eb8f7364d2e3f970a763987
ms.sourcegitcommit: f6a287a11480cbee99a2facda2590f3a744f7e45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53786749"
---
有关物理服务器复制的配置/进程服务器要求

**组件** | 要求 
--- | ---
硬件设置 | 
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
可用磁盘空间（进程服务器缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
 | 
软件设置 | 
操作系统 | Windows Server 2012 R2 <br> Windows Server 2016
操作系统区域设置 | 美国英语
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置。
IP 地址类型 | 静态 
| 
访问设置 | 
MYSQL | MySQL 应安装在配置服务器上。 可以手动安装，或者让 Site Recovery 在部署期间进行安装。 为安装 Site Recovery，请检查计算机是否可以访问 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi。
URL | 配置服务器需要访问这些 URL（直接或通过代理）：<br/><br/> Azure AD：``login.chinacloudapi.cn``；``login.microsoftonline.us``；``*.accesscontrol.chinacloudapi.cn``<br/><br/> 复制数据传输：``*.backup.windowsazure.cn``；``*.backup.windowsazure.us``<br/><br/> 复制管理：``*.hypervrecoverymanager.windowsazure.cn``；``*.hypervrecoverymanager.windowsazure.us``；``https://management.chinacloudapi.cn``；``*.services.visualstudio.com``<br/><br/> 存储访问：``*.blob.core.chinacloudapi.cn``；``*.blob.core.usgovcloudapi.net``<br/><br/> 时间同步：``time.nist.gov``；``time.windows.com<br/><br/> Telemetry (optional): ``dc.services.visualstudio.com``
防火墙 | 基于 IP 地址的防火墙规则应允许与 Azure URL 通信。 为了简化和限制 IP 范围，建议使用 URL 筛选。<br/><br/>对于商用 IP：<br/><br/>- 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/confirmation.aspx?id=57062)和 HTTPS (443) 端口。<br/><br/> - 允许中国北部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> - 允许订阅的 Azure 区域的 IP 地址范围以支持 Azure Active Directory、备份、复制和存储所需的 URL。<br/><br/> 对于政府 IP：<br/><br/> - 允许 Azure 政府数据中心 IP 范围和 HTTPS (443) 端口。<br/><br/> - 允许所有 US Gov 区域（弗吉尼亚州、德克萨斯州、亚利桑那州和爱荷华州）的 IP 地址范围以支持 Azure Active Directory、备份、复制和存储所需的 URL。
端口 | 允许 443（控制通道协调）<br/><br/> 允许 9443（数据传输） 

配置/进程服务器大小要求

CPU | 内存 | 缓存磁盘 | 数据更改率 | 复制的计算机
--- | --- | --- | --- | ---
8 个 vCPU<br/><br/> 2 个插槽 * 4 个核心 \@ 2.5 GHz | 16GB | 300 GB | 500 GB 或更少 | < 100 台计算机
12 个 vCPU<br/><br/> 2 个插槽 * 6 个核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机
16 个 vCPU<br/><br/> 2 个插槽 * 8 个核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 台计算机

<!-- Update_Description: new articles on site recovery config server reqs physical -->
<!--ms.date: 09/30/2018-->