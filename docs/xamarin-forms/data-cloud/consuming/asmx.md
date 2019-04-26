---
title: 使用 ASP.NET Web 服务 (ASMX)
description: 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 71fb2b4742a66a560541febc9dbe87aed503da4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328629"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服务 (ASMX)

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX 能够构建使用简单对象访问协议 (SOAP) 发送消息的 web 服务。SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。它们只需了解如何发送和接收 SOAP 消息。本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。_

SOAP 消息是包含以下元素的 XML 文档：

- 名为的根元素*信封*，它标识为 SOAP 消息的 XML 文档。
- 一个可选*标头*包含特定于应用程序的信息，例如身份验证数据的元素。 如果*标头*存在元素，则它必须是第一个子元素*信封*元素。
- 所需*正文*包含适用于接收方的 SOAP 消息的元素。
- 一个可选*容错*元素，用于指示错误消息。 如果*容错*元素不存在，必须为的子元素*正文*元素。

SOAP 可以在许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 上操作。 但是，一个 ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这包括对很多标准的 ASMX 服务配置的支持。

此示例包括在物理或模拟设备运行的移动应用程序和 ASMX 服务所提供的方法来获取、 添加、 编辑和删除数据。 移动应用程序运行时，它们连接到本地托管 ASMX 服务如以下屏幕截图中所示：

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

有关在应用程序中使用的数据模型的详细信息，请参阅[为数据建模](~/xamarin-forms/data-cloud/walkthrough.md)。

## <a name="create-the-todoservice-proxy"></a>创建 TodoService 代理

一个名为的代理类`TodoService`，扩展了`SoapHttpClientProtocol`并提供通过 HTTP 与 ASMX 服务进行通信的方法。 通过将对每个特定于平台的项目的 web 引用添加在 Visual Studio 2019 或 Visual Studio 2017 中生成代理。 Web 引用生成的方法和服务的 Web 服务描述语言 (WSDL) 文档中定义每个操作的事件。

例如，`GetTodoItems`服务操作都会导致`GetTodoItemsAsync`方法和一个`GetTodoItemsCompleted`代理中的事件。 生成的方法具有 void 返回类型并调用`GetTodoItems`的父操作`SoapHttpClientProtocol`类。 调用的方法从服务收到响应后，它将引发`GetTodoItemsCompleted`事件，并提供响应数据中的事件`Result`属性。

## <a name="create-the-isoapservice-implementation"></a>创建 ISoapService 实现

若要启用要使用的服务的共享的跨平台项目，此示例定义`ISoapService`接口，它遵循[中的任务的异步编程模型C# ](/dotnet/csharp/programming-guide/concepts/async/)。 每个平台实现`ISoapService`公开特定于平台的代理。 此示例使用`TaskCompletionSource`对象来公开为任务的异步接口的代理。 详细了解如何使用`TaskCompletionSource`在以下各节中找到的每个操作类型实现中。

该示例`SoapService`:

1. 实例化`TodoService`作为类级实例
1. 创建一个名为集合`Items`来存储`TodoItem`对象
1. 指定了可选的自定义终结点`Url`属性 `TodoService`

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

此方法创建一个新`ASMService.TodoItem`实例，并将每个属性设置为相同的属性从`TodoItem`实例。

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

此方法从生成的代理中检索数据`TodoItem`类型并将其设置在新创建`TodoItem`实例。

### <a name="retrieve-data"></a>检索数据

`ISoapService`接口需要`RefreshDataAsync`方法以返回`Task`与项集合。 但是，`TodoService.GetTodoItemsAsync`方法返回 void。 若要满足接口模式，必须调用`GetTodoItemsAsync`，等待`GetTodoItemsCompleted`事件触发，并填充的集合。 这样，你可以返回到 UI 的有效的集合。

下面的示例创建一个新`TaskCompletionSource`，开始中的异步调用`RefreshDataAsync`方法，并等待`Task`提供的`TaskCompletionSource`。 当`TodoService_GetTodoItemsCompleted`调用事件处理程序使用它来填充`Items`集合和更新`TaskCompletionSource`:

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

有关详细信息，请参阅[异步编程模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)并[TPL 和传统.NET Framework 异步编程](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>创建或编辑数据

当创建或编辑数据时，必须实现`ISoapService.SaveTodoItemAsync`方法。 此方法检测到是否`TodoItem`是一种新的或更新项并调用适当的方法上`todoService`对象。 `CreateTodoItemCompleted`并`EditTodoItemCompleted`应还实现事件处理程序，以了解何时`todoService`收到响应后从 ASMX 服务 （这些配置可以并入单个处理程序由于它们执行相同的操作）。 下面的示例演示了接口和事件处理程序实现，并将`TaskCompletionSource`对象，用于以异步方式运行：

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

删除数据需要类似的实现。 定义`TaskCompletionSource`，实现一个事件处理程序和`ISoapService.DeleteTodoItemAsync`方法：

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

测试与本地托管服务的物理或模拟设备需要自定义 IIS 配置、 终结点地址和防火墙规则，准备就绪。 有关如何设置用于测试环境的更多详细信息，请参阅[配置为 IIS Express 的远程访问](wcf.md#configure-remote-access-to-iis-express)。 测试 WCF 和 ASMX 之间唯一的区别是 TodoService 的端口号。

## <a name="related-links"></a>相关链接

- [TodoASMX （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
