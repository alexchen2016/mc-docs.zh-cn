---
title: 在 Azure Service Fabric 中参数化配置文件 | Azure
description: 了解如何在 Service Fabric 中参数化配置文件。
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 10/09/2018
ms.date: 06/03/2019
ms.author: v-yeche
ms.openlocfilehash: 5e9eccc5789bec8c9eac9fdad2feecae59f3e96a
ms.sourcegitcommit: d75eeed435fda6e7a2ec956d7c7a41aae079b37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195367"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>如何在 Service Fabric 中参数化配置文件

本文演示如何在 Service Fabric 中参数化配置文件。  如果还不熟悉管理多个环境的应用程序的核心概念，请阅读[管理多个环境的应用程序](service-fabric-manage-multiple-environment-app-configuration.md)。

## <a name="procedure-for-parameterizing-configuration-files"></a>参数化配置文件的过程

在此示例中，在应用程序部署中使用参数来替代配置值。

1. 打开你的服务项目中的 *\<MyService>\PackageRoot\Config\Settings.xml* 文件。
2. 通过添加以下 XML，设置配置参数名称和值，例如高速缓存大小等于 25：

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

3. 保存并关闭该文件。
4. 打开 *\<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* 文件。
5. 在 ApplicationManifest.xml 文件的 `Parameters` 元素中声明参数和默认值。  建议参数名称包含服务的名称（例如，“MyService”）。

    ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
    ```
6. 在 ApplicationManifest.xml 文件的 `ServiceManifestImport` 节中，添加 `ConfigOverride` 元素，引用配置包、节和参数。

    ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
    ```

> [!NOTE]
> 在添加 ConfigOverride 的情况下，Service Fabric 将始终选择应用程序参数或应用程序清单中指定的默认值。
>
>

## <a name="next-steps"></a>后续步骤
有关 Visual Studio 中其他可用应用管理功能的信息，请参阅[在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)。

<!-- Update_Description: update meta properties, wording update  -->