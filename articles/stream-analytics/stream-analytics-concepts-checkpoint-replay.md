---
title: Azure 流分析中的检查点和重播作业恢复概念
description: 本文介绍 Azure 流分析中的检查点和重播作业恢复概念。
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/12/2018
ms.date: 08/20/2018
ms.openlocfilehash: 091d36b3ca3f9be1c80667ce728f3184531dae28
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52655352"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Azure 流分析作业中的检查点和重播概念
本文介绍 Azure 流分析中内部检查点和重播的概念及其对作业恢复的影响。 每当运行流分析作业时，都会在内部维护状态信息。 该状态信息定期保存在检查点中。 在某些情况下，如果发生作业失败或升级，则会使用检查点信息进行作业恢复。 在另一些情况下，检查点无法用于恢复，而必须使用重播。

## <a name="stateful-query-logicin-temporal-elements"></a>时态元素中的有状态查询逻辑
Azure 流分析作业的独有功能之一是执行有状态的处理，如开窗聚合、临时联接和临时分析函数。 当作业运行时，其中的每个运算符都会保存状态信息。 这些查询元素的最大窗口大小为 7 天。 

多个流分析查询元素中都出现了时态窗口的概念：
1. 开窗聚合（翻转窗口、跳跃窗口和滑动窗口 GROUP BY）

2. 时态联接 (JOIN with DATEDIFF)

3. 时态分析函数（ISFIRST、LAST 和 LAG with LIMIT DURATION）

## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>发生节点故障（包括 OS 升级）后进行作业恢复
每当运行流分析作业时，该作业将在内部横向扩展，以跨多个工作节点执行工作。 每个工作节点的状态每隔几分钟就会创建检查点一次，从而在发生故障时帮助系统恢复。

有时，给定的工作节点可能会发生故障，或者该工作节点发生操作系统升级。 为了自动恢复，流分析将获取一个新的正常节点，同时最新可用的检查点还原先前工作节点的状态。 若要恢复工作，必须运行少量的重播，以便从创建检查点的时间还原状态。 通常，还原间隙只有几分钟。 如果为作业选择了足够多的流单位，则重播应该很快就能完成。 

在完全并行的查询中，发生工作节点故障后保持同步所需的时间与以下值成正比：

[输入事件速率] x [间隙长度] / [处理分区数]

如果由于节点故障和 OS 升级而出现严重的处理延迟，请考虑将查询设为完全并行，并扩展作业以分配更多的流单位。 有关详细信息，请参阅[扩展 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)。

如果此类恢复过程正在进行，当前的流分析不会显示报告。

## <a name="job-recovery-from-a-service-upgrade"></a>服务升级后进行作业恢复 
Azure 偶尔会升级在 Azure 服务中运行流分析作业的二进制文件。 这时，用户正在运行的作业会升级到更新的版本，并且作业会自动重启。 

目前，每次升级后，恢复检查点格式不会保留。 因此，必须完全使用重播方法来还原流查询的状态。 若要让流分析作业重播与以前完全相同的输入，必须至少将源数据的保留策略设置为查询中的窗口大小。 否则可能会导致服务升级期间出现错误或不完整的结果，因为源数据的保留时限并不足够靠后，以致不能包含整个窗口大小。

一般而言，所需的重播量与窗口大小乘以平均事件速率的结果成正比。 例如，如果某个作业的输入速率为每秒 1000 个事件，则大于 1 小时的窗口大小被认为是一个较大的重播大小。 为了生成完整正确的结果，最多可能需要重新处理一小时的数据，才能初始化状态，而这可能会导致输出延迟（无输出）更长时间。 无窗口或其他时态运算符（例如 `JOIN` 或 `LAG`）的查询不会重播。

## <a name="estimate-replay-catch-up-time"></a>估算重播同步时间
若要估算服务升级导致的延迟的长度，可以遵循以下方法：

1. 以预期的事件速率在输入事件中心加载足够的数据，以涵盖查询中的最大窗口大小。 在此整个时间段内，事件时间戳应该接近挂钟时间，就如同事件是实时输入源一样。 例如，如果查询中包含 3 天的时间窗口，请向事件中心发送事件 3 天，然后继续发送事件。 

2. 使用 **Now** 作为开始时间启动作业。 

3. 测量开始时间与生成第一个输出时的间隔时间。 此间隔时间大致是服务升级期间作业的延迟时间。

4. 如果延迟过长，请尝试将作业分区并增加 SU 数目，使负载分散到更多节点。 或者，考虑减小查询中的窗口大小，并对下游接收器中流分析作业生成的输出执行进一步的聚合或其他有状态处理（例如，使用 Azure SQL 数据库）。

为了克服升级任务关键型作业期间服务稳定性的一般忧虑，请考虑在配对的 Azure 区域中运行重复的作业。 有关详细信息，请参阅[在服务更新期间保证流分析作业可靠性](stream-analytics-job-reliability.md)。

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>在用户发起的停止和启动后进行作业恢复
若要编辑流作业的查询语法或调整输入和输出，需要停止该作业以进行更改，并升级作业设计。 在这种情况下，当用户停止流作业并再次将其启动时，恢复方案类似于服务升级。 

检查点数据不可用于用户发起的作业重启。 若要估算执行此类重启期间发生的输出延迟，请使用上一部分所述的相同过程；如果延迟太长，请应用类似的缓解措施。

## <a name="next-steps"></a>后续步骤
有关可靠性和可伸缩性的详细信息，请参阅以下文章：
- [教程：为 Azure 流分析作业设置警报](stream-analytics-set-up-alerts.md)
- [扩展 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)
- [在服务更新期间保证流分析作业可靠性](stream-analytics-job-reliability.md)
<!-- Update_Description: update meta properties  -->
