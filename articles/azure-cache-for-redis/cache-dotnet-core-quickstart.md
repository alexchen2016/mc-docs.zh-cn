---
title: 快速入门：了解如何将 Azure Redis 缓存与 .NET Core 应用配合使用 | Microsoft Docs
description: 在本快速入门中，了解如何在 .NET Core 应用中访问 Azure Redis 缓存
services: cache,app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: dotnet
ms.topic: quickstart
origin.date: 05/18/2018
ms.date: 03/27/2019
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: 4cb9ec46d4ead6cc86b30d09d77e6c5dbf867887
ms.sourcegitcommit: c5599eb7dfe9fd5fe725b82a861c97605635a73f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505485"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-core-app"></a>快速入门：将 Azure Redis 缓存与 .NET Core 应用配合使用



本快速入门展示了如何开始将 Azure Redis 缓存与 .NET Core 配合使用。 Azure Redis 缓存基于热门的开源 Azure Redis 缓存。 它提供对 Microsoft 所管理的安全专用的 Azure Redis 缓存的访问权限。 使用 Azure Redis 缓存创建的缓存可从 Azure 内的任何应用程序进行访问。

在本快速入门中，你将在 .NET Core 控制台应用中将 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 客户端与 C\# 代码配合使用。 你将创建缓存并配置 .NET Core 客户端应用。 然后，你将在缓存中添加和更新对象。 

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

![已完成的控制台应用](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

* [.NET SDK 2.0](https://www.microsoft.com/net/learn/get-started/windows) 或更高版本。
* StackExchange.Redis 客户端需要 [.NET Framework 4 或更高版本](https://www.microsoft.com/net/download/dotnet-framework-runtime)。

## <a name="create-a-cache"></a>创建缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

记下**主机名**和**主**访问密钥。 稍后将使用这些值来构造 *CacheConnection* 机密。



## <a name="create-a-console-app"></a>创建控制台应用

打开一个新的命令窗口并执行以下命令来创建新的.NET Core 控制台应用：

```
dotnet new console -o Redistest
```

在命令窗口中，更改到新的 *Redistest* 项目目录。



## <a name="add-secret-manager-to-the-project"></a>向项目添加机密管理器

在本部分中，将向项目添加[机密管理器工具](https://docs.microsoft.com/aspnet/core/security/app-secrets)。 机密管理器工具存储敏感数据，以便用于项目树之外的开发工作。 此方法有助于防止意外共享源代码中的应用密码。

打开 *Redistest.csproj* 文件。 添加 `DotNetCliToolReference` 元素以包含 Microsoft.Extensions.SecretManager.Tools。 同时添加 `UserSecretsId` 元素（如下所示），并保存文件。

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
    <UserSecretsId>Redistest</UserSecretsId>
  </PropertyGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
  </ItemGroup>
</Project>
```

执行以下命令来将 *Microsoft.Extensions.Configuration.UserSecrets* 包添加到项目：

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

执行以下命令来还原包：

```
dotnet restore
```

在命令窗口中，在替换你的缓存名称和主访问密钥的占位符（包括尖括号）后，执行以下命令来存储名为 *CacheConnection* 的新机密：

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.chinacloudapi.cn,abortConnect=false,ssl=true,password=<primary-access-key>"
```

向 *Program.cs* 中添加以下 `using` 语句：

```csharp
using Microsoft.Extensions.Configuration;
```

将以下成员添加到 *Program.cs* 中的 `Program` 类。 此代码初始化配置以访问 Azure Redis 缓存连接字符串的用户机密。

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>配置缓存客户端

在本部分中，你将配置控制台应用程序来将 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 客户端用于 .NET。

在命令窗口中，在 *Redistest* 项目目录中执行以下命令：

```
dotnet add package StackExchange.Redis
```

安装完成后，*StackExchange.Redis* 缓存客户端可供用于你的项目。


## <a name="connect-to-the-cache"></a>连接到缓存

向 *Program.cs* 中添加以下 `using` 语句：

```csharp
using StackExchange.Redis;
```

与 Azure Redis 缓存的连接由 `ConnectionMultiplexer` 类管理。 应当在整个客户端应用程序中共享并重复使用此类。 不要为每个操作都创建新连接。 

在 *Program.cs* 中，将以下成员添加到控制台应用程序的 `Program` 类：

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = Configuration[SecretName];
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

此方式在应用程序中共享 `ConnectionMultiplexer` 实例，它使用返回所连接的实例的静态属性。 该代码提供了一种线程安全方式，它仅初始化单个已连接的 `ConnectionMultiplexer` 实例。 `abortConnect` 设置为 false，这意味着即使未建立与 Azure Redis 缓存的连接，调用也会成功。 `ConnectionMultiplexer` 的一个关键功能是，一旦解决网络问题和其他原因，它便会自动还原缓存连接。

*CacheConnection* 机密的值是使用机密管理器配置提供程序访问的，并用作 password 参数。

## <a name="executing-cache-commands"></a>执行缓存命令

在 *Program.cs* 中，为控制台应用程序的 `Program` 类的 `Main` 过程添加以下代码：

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = lazyConnection.Value.GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

    lazyConnection.Value.Dispose();
}
```

保存 *Program.cs*。

Azure Redis 缓存具有可配置的数据库数量（默认为 16 个），因此可以通过逻辑方式隔离 Azure Redis 缓存中的数据。 该代码将连接到默认数据库 DB 0。 有关详细信息，请参阅[什么是 Redis 数据库？](cache-faq.md#what-are-redis-databases)和[默认 Redis 服务器配置](cache-configure.md#default-redis-server-configuration)。

可以使用 `StringSet` 和 `StringGet` 方法来存储和检索缓存项。

Redis 将大多数数据存储为 Redis 字符串，但这些字符串可能包含许多类型的数据，包括序列化的二进制数据，可在缓存中存储 .NET 对象时使用。

在命令窗口中执行以下命令来生成应用：

```
dotnet build
```

然后使用以下命令来运行应用：

```
dotnet run
```

在以下示例中可以看到，`Message` 键事先已包含一个缓存值，该值是使用 Azure 门户中的 Redis 控制台设置的。 应用更新了该缓存值。 应用还执行了 `PING` 和 `CLIENT LIST` 命令。

![控制台应用的一部分](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>处理缓存中的 .NET 对象

Azure Redis 缓存可以缓存 .NET 对象以及基元数据类型，但在缓存 .NET 对象之前，必须将其序列化。 此 .NET 对象序列化是应用程序开发人员的责任，同时赋与开发人员选择序列化程序的弹性。

将对象序列化的一种简单方式是使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) 中的 `JsonConvert` 序列化方法，并以 JSON 为源和目标进行序列化。 在本部分中，将向缓存添加一个 .NET 对象。

执行以下命令来将 *Newtonsoft.json* 包添加到应用：

```
dotnet add package Newtonsoft.json
```

将下面的 `using` 语句添加到 *Program.cs* 的开头：

```csharp
using Newtonsoft.Json;
```

将下面的 `Employee` 类定义添加到 *Program.cs* 中：

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string EmployeeId, string Name, int Age)
    {
        this.Id = EmployeeId;
        this.Name = Name;
        this.Age = Age;
    }
}
```

在 *Program.cs* 中 `Main()` 过程的底部并且在 `Dispose()` 调用之前，添加以下代码行来缓存和检索序列化的 .NET 对象：

```csharp
// Store .NET object to cache
Employee e007 = new Employee("007", "Davide Columbo", 100);
Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

// Retrieve .NET object from cache
Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
Console.WriteLine("Deserialized Employee .NET object :\n");
Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

保存 *Program.cs* 并使用以下命令重新生成应用：

```
dotnet build
```

使用以下命令运行应用来测试 .NET 对象的序列化：

```
dotnet run
```

![已完成的控制台应用](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>清理资源

如果想要继续学习下一篇教程，可以保留本快速入门中创建的资源，以便重复使用。

如果已完成快速入门示例应用程序，可以删除本快速入门中创建的 Azure 资源，以免产生费用。 

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，可从各自的边栏选项卡逐个删除这些资源，而不要删除资源组。
>

登录到 [Azure 门户](https://portal.azure.cn)，并单击“资源组”。

在“按名称筛选...”文本框中键入资源组的名称。 本文的说明使用了名为 *TestResources* 的资源组。 在结果列表中的资源组上，单击“...”，然后单击“删除资源组”。

![删除](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后单击“删除”。

片刻之后，将会删除该资源组及其包含的所有资源。



<a name="next-steps"></a>

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 .NET Core 应用程序使用 Azure Redis 缓存。 请继续学习下一个快速入门，将 Azure Redis 缓存与 ASP.NET Web 应用配合使用。

> [!div class="nextstepaction"]
> [创建使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)





<!-- Update_Description: wording update -->