---
title: Azure Monitor 中的 Azure Log Analytics VM 扩展故障排除 | Azure Docs
description: 针对 Windows 和 Linux Azure VM 的 Log Analytics VM 扩展的最常见问题，描述症状、原因和解决方法。
services: log-analytics
documentationcenter: ''
author: lingliw
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/19
ms.author: v-lingwu
ms.openlocfilehash: 656c886aa3e5163f57f743168069c2680ce6ca7f
ms.sourcegitcommit: bf3df5d77e5fa66825fe22ca8937930bf45fd201
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59686434"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics VM 扩展故障排除
本文可帮助排查使用世纪互联 Azure 上运行的 Windows 和 Linux 虚拟机的 Log Analytics VM 扩展时可能遇到的错误，并建议解决这些问题可能的解决方案。

若要验证扩展的状态，请从 Azure 门户执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“虚拟机”。 开始键入时，会根据输入筛选该列表。 选择“虚拟机”。
3. 在虚拟机列表中，找到并选择该虚拟机。
3. 在虚拟机上，单击“扩展”。
4. 从列表中，查看是否已启用 Log Analytics 扩展。  对于 Linux，代理列出为 **OMSAgentforLinux**；对于 Windows，代理列出为 **MicrosoftMonitoringAgent**。

   ![VM 扩展视图](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 单击要查看详细信息的扩展。 

   ![VM 扩展详细信息](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 扩展故障排除

如果 *Microsoft Monitoring Agent* VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步骤检查是否已安装 Azure VM 代理或者其是否正常工作。
   * 还可以查看 VM 代理日志文件 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果此日志不存在，则未安装 VM 代理。
   * [安装 Azure VM 代理](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 使用以下步骤确认 Azure Monitoring Agent 扩展检测信号任务正在运行：
   * 登录到虚拟机
   * 打开任务计划程序并找到 `update_azureoperationalinsight_agent_heartbeat` 任务
   * 确认任务已启用并且一直在运行
   * 查看 `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` 中的检测信号日志文件
3. 查看 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` 中的 Azure Monitoring Agent VM 扩展日志文件
4. 确保虚拟机可以运行 PowerShell 脚本
5. 确保 C:\Windows\temp 上的权限未被更改
6. 通过在虚拟机上的 PowerShell 特权窗口中键入以下内容来查看 Azure Monitoring Agent 的状态：`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. 查看 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` 中的 Azure Monitoring Agent 安装日志文件


## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 扩展故障排除
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
如果 Log Analytics Linux 代理 VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 如果扩展状态为“未知”，则查看 VM 代理日志文件 `/var/log/waagent.log`，检查 Azure VM 代理是否已安装且可正常工作
   * 如果此日志不存在，则未安装 VM 代理。
   * [在 Linux VM 上安装 Azure VM 代理](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 对于其他不正常状态，请查看 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` 中的 Log Analytics Linux VM 代理扩展日志文件
3. 如果扩展状态正常，但是未上传数据，则查看 `/var/opt/microsoft/omsagent/log/omsagent.log` 中的 Log Analytics Linux 代理日志文件

## <a name="next-steps"></a>后续步骤

有关与托管在 Azure 外部计算机上的 Log Analytics Linux 代理相关的其他故障排除指南，请参阅 [Azure Log Analytics Linux 代理故障排除](agent-linux-troubleshoot.md)。  




