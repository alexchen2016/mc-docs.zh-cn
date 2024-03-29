---
title: 数据库安全性 - Azure Cosmos DB
description: 了解 Azure Cosmos DB 如何为数据提供数据库保护和数据安全性。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 05/23/2019
ms.date: 06/17/2019
ms.author: v-yeche
ms.openlocfilehash: 85dfbf260615cf9f6d74e07e1f380354e5968ce4
ms.sourcegitcommit: 43eb6282d454a14a9eca1dfed11ed34adb963bd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151454"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 安全性 - 概述

本文介绍了数据库安全最佳做法以及 Azure Cosmos DB 提供的关键功能，帮助你防范、检测和应对数据库入侵。

<a name="whats-new"></a>
## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB 在安全性方面有哪些新增功能

静态加密现已可用于所有 Azure 中国区域的 Azure Cosmos DB 中存储的文档和备份。 对于这些区域中的新客户和现有客户，会自动应用静态加密。 无需进行任何配置；可获得与以前（即知道使用静态加密可确保数据安全之前）一样的出色延迟、吞吐量、可用性和功能。

## <a name="how-do-i-secure-my-database"></a>如何保护我的数据库

数据安全性的责任由你、客户和数据库提供程序共同分担。 根据所选的数据库提供程序，要承担的责任大小将有所不同。 如果选择本地解决方案，则从终结点保护到硬件物理安全性的所有工作都由你负责 - 这不是一个轻松的任务。 如果选择 Azure Cosmos DB 等 PaaS 云数据库提供程序，要考虑的问题会明显减少。 下图摘自我们的 [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility)（云计算的责任分担）白皮书，显示了使用 Azure Cosmos DB 等 PaaS 提供程序时，责任会得到怎样的减轻。

<!-- Notice: update Microsoft to ours is Correct-->

![客户和数据库提供程序的责任](./media/database-security/nosql-database-security-responsibilities.png)

上图显示了高层级的云安全组件，但是，对于数据库解决方案，需要考虑到哪些具体的事项呢？ 如何对不同的解决方案进行比较？

建议根据以下要求查检表来比较数据库系统：

- 网络安全和防火墙设置
- 用户身份验证和精细用户控制
- 能够在多个区域复制数据来应对区域性故障
- 能够从一个数据中心故障转移到另一个数据中心
- 在数据中心内执行本地数据复制
- 自动数据备份
- 从备份还原已删除的数据
- 保护和隔离敏感数据
- 监视攻击
- 响应攻击
- 能够地域隔离数据以遵守数据监管限制
- 对受保护数据中心内的服务器实施物理保护
- 认证

以下要求看似理所当然，但最近发生的[大规模数据库入侵](https://thehackernews.com/2017/01/mongodb-database-security.html)提醒我们这些要求尽管很简单，但却至关重要：

- 让修补的服务器保持最新状态
- 按默认启用 HTTPS/SSL 加密
- 使用强密码的管理帐户

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Azure Cosmos DB 如何保护数据库

让我们回顾前面的列表 - Azure Cosmos DB 能够满足其中的多少项要求？ 它满足每一项要求。

让我们深入分析其中的每项要求。

|安全要求|Azure Cosmos DB 的安全方案|
|---|---|
|网络安全性|使用 IP 防火墙是用于保护数据库的第一个保护层。 Azure Cosmos DB 支持使用基于 IP 的策略驱动访问控制来提供入站防火墙支持。 基于 IP 的访问控制类似于传统数据库系统使用的防火墙规则，但已经过扩展，确保只能通过获批准的一组计算机或云服务访问 Azure Cosmos DB 数据库帐户。 <br /><br />使用 Azure Cosmos DB 可以启用特定的 IP 地址 (168.61.48.0)、IP 范围 (168.61.48.0/8) 以及 IP 和范围的组合。 <br /><br />从此允许列表之外的计算机发出的所有请求会被 Azure Cosmos DB 阻止。 从获批准计算机和云服务发出的请求必须完成身份验证过程才能获得资源的访问控制权。<br /><br />可以在 [Azure Cosmos DB 防火墙支持](firewall-support.md)中了解详细信息。|
|授权|Azure Cosmos DB 使用基于哈希的消息身份验证代码 (HMAC) 进行授权。 <br /><br />每个请求将使用机密帐户密钥进行哈希处理，后续的 base-64 编码哈希将连同每个调用发送到 Azure Cosmos DB。 要验证请求，Azure Cosmos DB 服务需使用正确的机密密钥和属性生成哈希值，然后将该值与请求中的值进行比较。 如果两个值匹配，则成功为操作授权并处理请求，否则，会发生授权失败并拒绝请求。<br /><br />可以使用[主密钥](secure-access-to-data.md#master-keys)或[资源令牌](secure-access-to-data.md#resource-tokens)对文档等资源进行精细访问。<br /><br />可以在[保护对 Azure Cosmos DB 资源的访问](secure-access-to-data.md)中了解详细信息。|
|用户和权限|使用帐户的主密钥可为每个数据库创建用户资源和权限资源。 资源令牌与数据库中的权限相关联，确定用户是否对数据库中的应用程序资源拥有访问权限（读写、只读或无访问权限）。 应用程序资源包括容器、文档、附件、存储过程、触发器和 UDF。 然后，在身份验证期间，使用资源令牌来允许或拒绝访问资源。<br /><br />可以在[保护对 Azure Cosmos DB 资源的访问](secure-access-to-data.md)中了解详细信息。|
|Active Directory 集成 (RBAC)| 还可以在 Azure 门户中通过“访问控制(标识和访问管理)”来提供或限制对 Cosmos 帐户、数据库、容器和套餐（吞吐量）的访问权限。 IAM 提供基于角色的访问控制并与 Active Directory 集成。 对于个人和组，可使用内置角色或自定义角色。 有关详细信息，请参阅 [Active Directory 集成](role-based-access-control.md)一文。|
|多区域复制|Azure Cosmos DB 提供统包式多区域分发。只需单击一下按钮，就能将数据复制到 Azure 的任何一个跨中国的数据中心。 多区域复制可以实现多区域缩放，以较低的延迟访问中国的数据。<br /><br />从安全角度来看，多区域复制可确保数据受到保护，防范区域性故障。<br /><br />若要了解详细信息，请参阅[在多个区域分配数据](distribute-data-globally.md)。|
|区域性故障转移|如果已将数据复制到多个数据中心，当区域数据中心脱机时，Azure Cosmos DB 会自动切换操作。 可以使用数据复制到的区域创建故障转移区域的优先级列表。 <br /><br />可以在 [Azure Cosmos DB 中的区域性故障转移](high-availability.md)中了解详细信息。|
|本地复制|即使是在单个数据中心内，Azure Cosmos DB 也会自动复制数据来实现高可用性，并允许选择[一致性级别](consistency-levels.md)。 此复制可保证为所有单区域帐户和具有松散一致性的所有多区域帐户提供 99.99% 的[可用性 SLA](https://www.azure.cn/support/sla/cosmos-db)，为所有多区域数据库帐户提供 99.999% 的读取可用性。|
|自动联机备份|Azure Cosmos DB 数据库定期备份并存储在异地冗余的存储中。 <br /><br />可以在[使用 Azure Cosmos DB 进行自动联机备份和还原](online-backup-and-restore.md)中了解详细信息。|
|还原已删除的数据|可以使用自动联机备份来恢复大约 30 天内意外删除的数据。 <br /><br />可以在[使用 Azure Cosmos DB 进行自动联机备份和还原](online-backup-and-restore.md)中了解详细信息|
|保护和隔离敏感数据|“新增功能”中列出的区域中的所有数据现已处于静态加密状态。<br /><br />可将个人数据和其他机密数据隔离到特定的容器，并限制为只能由特定的用户进行读写或只读访问。|
|监视攻击|使用[审核日志和活动日志](logging.md)，可以监视帐户中的正常和异常活动。 可以查看针对资源执行了哪些操作、操作是谁发起的、操作是何时发生的、操作的状态等，如此表后面的屏幕截图所示。|
|响应攻击|联系 Azure 支持部门举报潜在的攻击行为后，将启动由 5 个步骤构成的事件响应过程。 该 5 步骤过程的目的是在检测到问题并启动调查后，尽快将服务安全性和操作恢复正常。<br /><br />在[云中的 Azure 安全响应](https://aka.ms/securityresponsepaper)中了解详细信息。|
|地域隔离|Azure Cosmos DB 确保符合主权区域（例如德国、中国和美国政府）的数据治理要求。|
|受保护的设施|Azure Cosmos DB 中的数据存储在 Azure 的受保护数据中心内的 SSD 上。<br /><br /> |
|HTTPS/SSL/TLS 加密|从客户端到服务的所有 Azure Cosmos DB 交互都支持 SSL/TLS 1.2。 此外，所有数据中心内部和跨数据中心的复制都会实施 SSL/TLS 1.2。|
|静态加密|对存储在 Azure Cosmos DB 中的所有静态数据进行加密。 可以在 [Azure Cosmos DB 静态加密](./database-encryption-at-rest.md)中了解详细信息。|
|修补的服务器|作为一种托管的数据库，在 Azure Cosmos DB 中无需管理和修补服务器，系统会自动完成这些操作。|
|使用强密码的管理帐户|难以相信，我们竟然还要提到这项要求。但与我们的某些竞争产品不同，在 Azure Cosmos DB 中，不带密码的管理帐户是根本不受允许的。<br /><br /> DocumentDB 中默认融入了基于 SSL 和 HMAC 机密的身份验证安全性。|
|安全和数据保护认证| 有关认证的最新数据列表，请参阅具有所有认证（搜索 Cosmos）的整个 [Azure 符合性站点](https://www.trustcenter.cn/zh-cn/compliance/)以及最新 [Azure 符合性文档](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。 如需更有针对性的阅读，请查看 2018 年 4 月 25 日的帖子 [Azure #CosmosDB:Secure, private, compliant]（Azure #CosmosDB：安全性、隐私性、符合性），其中包含 SOCS 1/2 类型 2、HITRUST、PCI DSS 1 级、ISO 27001、HIPAA、FedRAMP High 和许多其他内容。

<!-- MOONCAKE: CORRECT ON https://www.trustcenter.cn/zh-cn/compliance/-->
<!-- Line: 72 Notice: 全球分布 to 多个区域分布 -->
<!-- Line: 72 Notice  任何一个全球数据中心 to 多个数据中心之中任何一个 -->
<!-- Line: 72 Notice  全球各地的数据 to 各个区域的数据 -->
<!-- Line: 83 Not Available on 在 [Microsoft 全球数据中心](https://www.microsoft.com/cloud-platform/global-datacenters) -->

以下屏幕截图显示如何使用审核日志记录和活动日志监视帐户：![Azure Cosmos DB 的活动日志](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>后续步骤

有关主密钥和资源令牌的详细信息，请参阅[保护对 Azure Cosmos DB 数据的访问](secure-access-to-data.md)。

有关审核日志记录的详细信息，请参阅 [Azure Cosmos DB 诊断日志记录](logging.md)。

有关我们的认证的详细信息，请参阅 [Azure 信任中心](https://www.trustcenter.cn/zh-cn/cloudservices/azure.html)。

<!--Update_Description: update meta properties, wording update -->