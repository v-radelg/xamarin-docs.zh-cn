---
title: 使用 Windows Communication Foundation (WCF) Web 服务
description: 本文演示如何使用 Xamarin.Forms 应用程序中的 WCF 简单对象访问协议 (SOAP) 服务。
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: d170e37b8bf4ce880f9d8f48d30defb42ee6bba2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648013"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>使用 Windows Communication Foundation (WCF) Web 服务

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。它允许开发人员构建安全、 可靠、 事务处理，且可互操作分布式应用程序。本文演示如何使用 Xamarin.Forms 应用程序中的 WCF 简单对象访问协议 (SOAP) 服务。_

WCF 描述了具有各种不同协定的服务, 包括:

- **数据协定**– 定义构成对其中一条消息的内容的基础的数据结构。
- **消息协定搭配**– 撰写邮件从现有数据协定。
- **错误协定**– 允许指定的自定义 SOAP 错误。
- **服务协定**– 指定服务支持的操作，这些消息所需的与每个操作进行交互。 它们还指定可以与每个服务上的操作相关联的任何自定义错误行为。

ASP.NET Web Services (.ASMX) 和 WCF 之间存在差异, 但 WCF 支持与通过 HTTP 提供的 SOAP 消息相同的功能。 有关使用 .ASMX 服务的详细信息, 请参阅[使用 ASP.NET Web 服务 (.asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md)。

> [!IMPORTANT]
> 对 WCF 的 Xamarin 平台支持仅限于使用`BasicHttpBinding`类通过 HTTP/HTTPS 进行文本编码的 SOAP 消息。
>
> WCF 支持需要使用仅在 Windows 环境中可用的工具来生成代理并托管 TodoWCFService。 生成和测试 iOS 应用需要在 Windows 计算机上部署 TodoWCFService, 或将其部署为 Azure web 服务。
>
> Xamarin 窗体本机应用通常与 .NET Standard 类库共享代码。 但是, .NET Core 当前不支持 WCF, 因此共享项目必须是旧的可移植类库。 有关 .NET Core 中 WCF 支持的信息, 请参阅[为服务器应用选择 .Net core 和 .NET Framework](/dotnet/standard/choosing-core-framework-server)。

示例应用程序解决方案包括一个可在本地运行的 WCF 服务, 如以下屏幕截图所示:

![](wcf-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
>
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>使用 web 服务

WCF 服务提供了以下操作：

|操作|描述|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关在应用程序中使用的数据模型的详细信息，请参阅[为数据建模](~/xamarin-forms/data-cloud/web-services/introduction.md)。

一个*代理*必须生成以使用 WCF 服务，允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据中生成的 web 服务的 Web 服务描述语言 (WSDL) 文档的形式公开。 在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider，若要将 web 服务的服务引用添加到.NET Standard 库，可以生成代理。 创建的代理帐户在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider 的替代方法是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 有关详细信息，请参阅[ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/)。

生成的代理类提供用于使用 web 服务使用异步编程模型 (APM) 设计模式的方法。 在此模式下，异步操作实现这两个方法名为*BeginOperationName*并*EndOperationName*，其中开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用线程上执行指令，同时异步操作在线程池线程。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 例如，`EndGetTodoItems`方法返回的集合`TodoItem`实例。 *EndOperationName*方法还包括`IAsyncResult`应设置为的对应调用返回的实例的参数*BeginOperationName*方法。

任务并行库 (TPL) 可以简化使用 APM begin/end 方法对通过封装中相同的异步操作的过程`Task`对象。 这种封装提供的多个重载`TaskFactory.FromAsync`方法。

有关 APM 的详细信息请参阅[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)并[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="create-the-todoserviceclient-object"></a>创建 TodoServiceClient 对象

生成的代理类提供了`TodoServiceClient`类，该类用于通过 HTTP 与 WCF 服务进行通信。 它提供用于调用 web 服务方法从 URI 的异步操作来标识服务实例的功能。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`TodoServiceClient`实例在类级别声明的以便在对象存在的只要该应用程序需要使用 WCF 服务，如下面的代码示例中所示：

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

`TodoServiceClient`实例配置有绑定的绑定信息以及终结点地址。 使用绑定指定传输、 编码和协议详细信息所需的应用程序和服务彼此通信。 `BasicHttpBinding`指定将通过 HTTP 传输协议发送文本编码的 SOAP 消息。 指定终结点地址可以连接到 WCF 服务的不同实例应用程序，前提是有多个已发布的实例。

有关配置服务引用的详细信息，请参阅[配置服务引用](~/cross-platform/data-cloud/web-services/index.md#wcf)。

### <a name="create-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用`TodoItem`模型数据的类。 用于存储`TodoItem`中 web 服务必须首先将转换为生成的代理项`TodoItem`类型。 这通过实现`ToWCFServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

此方法只需创建一个新`TodoWCFService.TodoItem`实例，并将每个属性设置为相同的属性从`TodoItem`实例。

同样，当从 web 服务检索数据时，必须将它从转换生成的代理`TodoItem`键入到`TodoItem`实例。 这通过实现`FromWCFServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

此方法只需从生成的代理中检索数据`TodoItem`类型并将其设置在新创建`TodoItem`实例。

### <a name="retrieve-data"></a>检索数据

`TodoServiceClient.BeginGetTodoItems`并`TodoServiceClient.EndGetTodoItems`方法用于调用`GetTodoItems`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成，具有`null`参数，该值指示不传递到任何数据`BeginGetTodoItems`委托。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

`TodoServiceClient.EndGetTodoItems`方法将返回`ObservableCollection`的`TodoWCFService.TodoItem`实例，然后将转换为`List`的`TodoItem`显示的实例。

### <a name="create-data"></a>创建数据

`TodoServiceClient.BeginCreateTodoItem`并`TodoServiceClient.EndCreateTodoItem`方法用于调用`CreateTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndCreateTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成，具有`todoItem`参数被传递到的数据`BeginCreateTodoItem`委托来指定`TodoItem`若要创建的 web 服务。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`FaultException`未能创建`TodoItem`，这由应用程序处理。

### <a name="update-data"></a>更新数据

`TodoServiceClient.BeginEditTodoItem`并`TodoServiceClient.EndEditTodoItem`方法用于调用`EditTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndEditTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成，具有`todoItem`参数被传递到的数据`BeginEditTodoItem`委托来指定`TodoItem`若要通过 web 服务进行更新。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`FaultException`如果无法找到或更新`TodoItem`，这由应用程序处理。

### <a name="delete-data"></a>删除数据

`TodoServiceClient.BeginDeleteTodoItem`并`TodoServiceClient.EndDeleteTodoItem`方法用于调用`DeleteTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndDeleteTodoItem`方法一次`TodoServiceClient.BeginDeleteTodoItem`方法完成，具有`id`参数被传递到的数据`BeginDeleteTodoItem`委托来指定`TodoItem`要删除 web 服务。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`FaultException`如果无法找到或删除`TodoItem`，这由应用程序处理。

## <a name="configure-remote-access-to-iis-express"></a>配置对 IIS Express 的远程访问
在 Visual Studio 2017 或 Visual Studio 2019 中, 你应该能够在没有其他配置的情况下在电脑上测试 UWP 应用程序。 测试 Android 和 iOS 客户端可能需要此部分中的其他步骤。 有关详细信息, 请参阅[从 IOS 模拟器和 Android 模拟器连接到本地 Web 服务](~/cross-platform/deploy-test/connect-to-local-web-services.md)。

默认情况下, IIS Express 仅响应对`localhost`的请求。 远程设备 (如 Android 设备, iPhone 甚至是模拟器) 将无法访问本地 WCF 服务。 需要在本地网络上了解 Windows 10 工作站的 IP 地址。 出于本示例的目的, 假设工作站有 IP 地址`192.168.1.143`。 以下步骤说明如何配置 Windows 10 和 IIS Express 以接受远程连接并从物理或虚拟设备连接到服务:

1. **向 Windows 防火墙添加例外**。 必须通过 Windows 防火墙打开端口, 子网中的应用程序才能使用这些应用程序与 WCF 服务进行通信。 在防火墙中创建入站规则打开端口49393。 在管理命令提示符下, 运行以下命令:
    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **将 IIS Express 配置为接受远程连接**。 您可以通过在 **[解决方案目录\.] vs\config\applicationhost.config**中编辑 IIS Express 的配置文件来配置 IIS Express。查找名称`site` `TodoWCFService`为的元素。 它应类似于以下 XML:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    需要添加两个`binding`元素以打开端口49393到外部流量和 Android 模拟器。 绑定使用一`[IP address]:[port]:[hostname]`种格式, 该格式指定 IIS Express 响应请求的方式。 外部请求的主机名必须指定为`binding`。 将以下 XML 添加到`bindings`元素, 并将 ip 地址替换为你自己的 ip 地址:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    更改后, `bindings`元素应如下所示:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >默认情况下, 出于安全原因, IIS Express 将不接受来自外部源的连接。 若要启用远程设备的连接, 必须使用管理权限运行 IIS Express。 执行此操作的最简单方法是运行具有管理权限的 Visual Studio 2017。 这会在运行 TodoWCFService 时, 通过管理权限启动 IIS Express。

    完成这些步骤后, 你应该能够运行 TodoWCFService 并从子网中的其他设备进行连接。 可以通过运行应用程序并访问`http://localhost:49393/TodoService.svc`来对此进行测试。 如果在访问该 URL 时收到错误**请求**错误, `bindings`则 IIS Express 配置中可能不正确 (请求 IIS Express, 但被拒绝)。 如果遇到其他错误, 可能是因为应用程序未运行或防火墙配置不正确。

    若要允许 IIS Express 继续运行和服务, 请关闭 "项目属性" 中的 "**编辑并继续**" 选项 **> Web > 调试器**。

1. **自定义终结点设备用于访问服务的终结点**。 此步骤包括配置在物理或模拟设备上运行的客户端应用程序, 以访问 WCF 服务。

    Android 模拟器使用内部代理, 阻止模拟器直接访问主机的`localhost`地址。 相反, 模拟器上`10.0.2.2`的地址通过内部代理路由`localhost`到主机上的。 这些代理的请求将`127.0.0.1`作为请求标头中的主机名, 因此, 在上述步骤中为此主机名创建 IIS Express 绑定的原因。

    IOS 模拟器在 Mac 生成主机上运行, 即使使用的是[适用于 Windows 的远程 IOS 模拟器](~/tools/ios-simulator/index.md)也是如此。 来自模拟器的网络请求将本地网络上的工作站 IP 作为主机名 (在本示例中, 实际的`192.168.1.143`IP 地址可能不同)。 这就是在上述步骤中为此主机名创建 IIS Express 绑定的原因。

    确保 TodoWCF `SoapUrl` (可移植) 项目中的**Constants.cs**文件中的属性的值对于你的网络是正确的:

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    使用适当的终结点配置**Constants.cs**后, 应能够从物理或虚拟设备连接到 Windows 10 工作站上运行的 TodoWCFService。

## <a name="related-links"></a>相关链接

- [TodoWCF （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [如何：创建 Windows Communication Foundation 客户端](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Svcutil.exe 元数据实用工具 ()](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
