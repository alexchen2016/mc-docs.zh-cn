---
title: Azure Service Fabric 服务模型 XML 架构简单类型 | Azure
description: 介绍 Service Fabric 服务模型的 XML 架构中的简单类型。
services: service-fabric
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
origin.date: 05/18/2018
ms.date: 05/28/2018
ms.author: v-yeche
ms.openlocfilehash: 26c5a434f60b07dd230332863a2ce0d702b0372f
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52661913"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-simple-types"></a>服务模型 XML 架构简单类型

## <a name="fabricuri-simpletype"></a>FabricUri simpleType
Azure Service Fabric 命名系统将其用作服务的稳定标识符的 URI。 

### <a name="xml-source"></a>XML 源
```xml
<xs:simpleType xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="FabricUri">
    <xs:annotation>
      <xs:documentation>A URI that is used as a stable identifier of services by Azure Service Fabric Naming system. </xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:anyURI"/>
  </xs:simpleType>

```
<!-- Update_Description: new articles on service fabric service model schema simple types  -->
<!--ms.date: 05/28/2018-->