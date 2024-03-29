---
title: 更改 Azure Service Fabric 执行组件中的 FabricTransport 设置 | Azure
description: 了解如何配置 Azure Service Fabric 执行组件通信设置。
services: Service-Fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 04/20/2017
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 62bd736f08766e683259329685a03a69c50388d7
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626492"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>配置 Reliable Actors 的 FabricTransport 设置

以下为用户可以配置的设置：
- C#:[FabricTransportRemotingSettings](https://docs.azure.cn/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java:[FabricTransportRemotingSettings](https://docs.azure.cn/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

可以通过以下方式修改 FabricTransport 的默认配置。

## <a name="assembly-attribute"></a>程序集属性

需要在执行组件客户端和执行组件服务程序集上应用 [FabricTransportActorRemotingProvider](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?view=azure-dotnet?redirectedfrom=MSDN) 属性。

以下示例演示如何更改 FabricTransport OperationTimeout 设置的默认值：

```csharp
using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
[assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
```

第二个示例更改 FabricTransport MaxMessageSize 和 OperationTimeoutInSeconds 的默认值。

```csharp
using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
[assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
```

## <a name="config-package"></a>配置包

可以使用[配置包](service-fabric-application-and-service-manifests.md)修改默认配置。

> [!IMPORTANT]
> 在 Linux 节点上，证书必须是 PEM 格式。 若要详细了解如何查找和配置适用于 Linux 的证书，请参阅[在 Linux 上配置证书](./service-fabric-configure-certificates-linux.md)。 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>配置执行组件服务的 FabricTransport 设置

在 settings.xml 文件中添加 TransportSettings 部分。

默认情况下，执行组件代码寻找“&lt;ActorName&gt;TransportSettings”形式的 SectionName。 如果未找到，则会查找“TransportSettings”形式的 SectionName。

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>配置执行组件客户端程序集的 FabricTransport 设置

如果客户端不是作为服务一部分运行，则可在客户端 .exe 文件所在的同一位置创建“&lt;Client Exe Name&gt;.settings.xml”文件。 然后，在该文件中添加 TransportSettings 部分。 SectionName 应为“TransportSettings”。

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* 使用辅助证书为安全执行组件服务/客户端配置 FabricTransport 设置。
  可通过添加参数 CertificateFindValuebySecondary 来添加辅助证书信息。
  以下是侦听器 TransportSettings 的示例。

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   以下是客户端 TransportSettings 的示例。

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * 通过使用者名称为安全执行组件服务/客户端配置 FabricTransport 设置。
    用户需提供 findType 作为 FindBySubjectName，并添加 CertificateIssuerThumbprints 和 CertificateRemoteCommonNames 值。
    以下是侦听器 TransportSettings 的示例。

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    以下是客户端 TransportSettings 的示例。

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  <!-- Update_Description: update meta properties, wording update, update link  -->