---
title: 使用 ASP.NET Web 服务 (.ASMX)
description: 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 124cffaf827ebeb8109f3cd4ac4dfd2701e43f9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656654"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服务 (.ASMX)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX 能够构建使用简单对象访问协议 (SOAP) 发送消息的 web 服务。SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。它们只需了解如何发送和接收 SOAP 消息。本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。_

SOAP 消息是包含以下元素的 XML 文档：

- 名为的根元素*信封*，它标识为 SOAP 消息的 XML 文档。
- 一个可选*标头*包含特定于应用程序的信息，例如身份验证数据的元素。 如果*标头*存在元素，则它必须是第一个子元素*信封*元素。
- 所需*正文*包含适用于接收方的 SOAP 消息的元素。
- 一个可选*容错*元素，用于指示错误消息。 如果*容错*元素不存在，必须为的子元素*正文*元素。

SOAP 可以在许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 上操作。 但是，一个 ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这包括对很多标准的 ASMX 服务配置的支持。

此示例包括在物理设备或模拟设备上运行的移动应用程序, 以及提供用于获取、添加、编辑和删除数据的方法的一个服务。 当移动应用程序运行时, 它们将连接到本地托管的 .ASMX 服务, 如以下屏幕截图所示:

![](asmx-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>使用 web 服务

ASMX 服务提供了以下操作：

|操作|描述|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关在应用程序中使用的数据模型的详细信息，请参阅[为数据建模](~/xamarin-forms/data-cloud/web-services/introduction.md)。

## <a name="create-the-todoservice-proxy"></a>创建 TodoService 代理

名`TodoService`为的代理类扩展`SoapHttpClientProtocol`并提供通过 HTTP 与 .asmx 服务进行通信的方法。 通过在 Visual Studio 2019 或 Visual Studio 2017 中添加对每个特定于平台的项目的 web 引用来生成代理。 Web 引用为服务的 Web 服务描述语言 (WSDL) 文档中定义的每个操作生成方法和事件。

例如, `GetTodoItems`服务操作将导致代理中的`GetTodoItemsAsync`方法和`GetTodoItemsCompleted`事件。 生成的方法的返回类型为 void, 并对`GetTodoItems`父`SoapHttpClientProtocol`类调用该操作。 当被调用的方法接收到来自服务的响应时, 它`GetTodoItemsCompleted`将触发事件, 并在事件的`Result`属性中提供响应数据。

## <a name="create-the-isoapservice-implementation"></a>创建 ISoapService 实现

若要启用共享的跨平台项目以使用该服务, 此示例定义`ISoapService`了接口, 该接口遵循中[ C#的任务异步编程模型](/dotnet/csharp/programming-guide/concepts/async/)。 每个平台都`ISoapService`实现以公开特定于平台的代理。 该示例使用`TaskCompletionSource`对象将代理作为任务异步接口公开。 有关使用`TaskCompletionSource`的详细信息, 请参阅以下各节中每种操作类型的实现。

示例`SoapService`:

1. `TodoService`实例化为类级别实例
1. 创建一个名`Items`为的集合`TodoItem` , 用于存储对象
1. 指定上的可选`Url`属性的自定义终结点`TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用`TodoItem`模型数据的类。 用于存储`TodoItem`中 web 服务必须首先将转换为生成的代理项`TodoItem`类型。 这通过实现`ToASMXServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
    return new ASMXService.TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

此方法创建一个新`ASMService.TodoItem`的实例, 并将每个属性设置为`TodoItem`实例中的相同属性。

同样，当从 web 服务检索数据时，必须将它从转换生成的代理`TodoItem`键入到`TodoItem`实例。 这通过实现`FromASMXServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
    return new TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

此方法从代理生成`TodoItem`的类型中检索数据, 并在新创建`TodoItem`的实例中设置数据。

### <a name="retrieve-data"></a>检索数据

接口要求方法返回`Task`具有项集合的。 `RefreshDataAsync` `ISoapService` 但是, 该`TodoService.GetTodoItemsAsync`方法返回 void。 若要满足接口模式, 必须调用`GetTodoItemsAsync`, 等待`GetTodoItemsCompleted`事件激发, 并填充集合。 这允许您将有效集合返回到 UI。

下面的示例创建一个新`TaskCompletionSource`的, `RefreshDataAsync`在方法中开始异步调用, 并`TaskCompletionSource`等待提供`Task`的。 在调用`Items` `TaskCompletionSource`事件处理程序时, 它将填充集合并更新: `TodoService_GetTodoItemsCompleted`

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

有关详细信息, 请参阅[异步编程模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)和[TPL 和传统 .NET Framework 异步编程](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>创建或编辑数据

创建或编辑数据时, 必须实现`ISoapService.SaveTodoItemAsync`方法。 此方法将检测是否`TodoItem`为新的或更新的项, 并对`todoService`对象调用适当的方法。 还应`EditTodoItemCompleted`实现`todoService`和事件处理程序, 以便您知道何时接收到来自 .asmx 服务的响应 (这些操作可以合并为单个处理程序, 因为它们执行相同的操作)。 `CreateTodoItemCompleted` 下面的示例演示接口和事件处理程序的实现, 以及用于异步`TaskCompletionSource`操作的对象:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>删除数据

删除数据需要类似实现。 定义, 实现事件处理程序`ISoapService.DeleteTodoItemAsync`和方法: `TaskCompletionSource`

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>测试 web 服务

使用本地托管的服务测试物理或模拟设备需要就地自定义 IIS 配置、终结点地址和防火墙规则。 有关如何设置环境以进行测试的更多详细信息, 请参阅[配置远程访问 IIS Express](wcf.md#configure-remote-access-to-iis-express)。 测试 WCF 和 .ASMX 之间唯一的区别是 TodoService 的端口号。

## <a name="related-links"></a>相关链接

- [TodoASMX （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
