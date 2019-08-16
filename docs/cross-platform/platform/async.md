---
title: 异步支持概述
description: 本文档介绍了如何对 async 和 await 进行编程, C#这是在5中引入的概念, 以便更轻松地编写异步代码。
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: a9297d9a19ef56d658e983c38329b1aa400ffd05
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521715"
---
# <a name="async-support-overview"></a>异步支持概述

_C#5引入了两个关键字, 用于简化异步编程: async 和 await。通过这些关键字, 可以编写简单的代码, 该代码利用任务并行库来执行另一个线程中长时间运行的操作 (例如网络访问), 并在完成后轻松访问结果。最新版本的 Xamarin 和 Xamarin 支持 async 和 await-本文档提供了说明, 并举例说明了如何结合使用新语法和 Xamarin。_

Xamarin 的异步支持构建在 Mono 3.0 基础之上, 并将 API 配置文件从升级为移动友好版本的 Silverlight, 作为 .NET 4.5 的移动友好版本。

## <a name="overview"></a>概述

本文档介绍新的 async 和 await 关键字, 并演练在 Xamarin 和 Xamarin 中实现异步方法的一些简单示例。

有关C# 5 的新异步功能 (包括许多示例和不同的使用方案) 的更完整讨论, 请参阅[异步编程](https://docs.microsoft.com/dotnet/csharp/async)一文。

该示例应用程序将创建一个简单的异步 web 请求 (不阻止主线程), 然后使用下载的 html 和字符计数更新 UI。

 [![](async-images/AsyncAwait_427x368.png "该示例应用程序会发出简单的异步 web 请求, 而不会阻止主线程, 然后使用下载的 html 和字符计数来更新 UI。")](async-images/AsyncAwait.png#lightbox)

Xamarin 的异步支持构建在 Mono 3.0 基础之上, 并将 API 配置文件从升级为移动友好版本的 Silverlight, 作为 .NET 4.5 的移动友好版本。

## <a name="requirements"></a>要求

C#5功能需要在 Xamarin 6.4 和 Xamarin 4.8 中包含的 Mono 3.0。 系统将提示您升级 Mono、Xamarin、Xamarin 和 Xamarin, 以利用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async`和`await`是与C#任务并行库结合使用的新语言功能, 便于编写线程代码以执行长时间运行的任务, 而不会阻止应用程序的主线程。

## <a name="async"></a>async

### <a name="declaration"></a>声明

`async`关键字放置在方法声明 (或 lambda 或匿名方法) 中, 以指示它包含可异步运行的代码, 即不阻止调用方的线程。

标记为的`async`方法应包含至少一个 await 表达式或语句。 如果方法`await`中不存在, 则它将以同步方式运行 (与没有`async`修饰符的情况相同)。 这也会导致编译器警告 (而不是错误)。

### <a name="return-types"></a>返回类型

异步方法应返回`Task` `Task<TResult>`或`void`。

如果方法不返回任何其他值, 则指定返回类型。`Task`

如果`Task<TResult>`方法需要返回值, 则指定, 其中`TResult`是要`int`返回的类型 (例如)。

`void`返回类型主要用于需要它的事件处理程序。 调用返回 void 的异步方法的代码不`await`能出现在结果上。

### <a name="parameters"></a>参数

异步方法不能`ref`声明`out`或参数。

## <a name="await"></a>await

Await 运算符可应用于标记为 async 的方法中的任务。 这会使方法在该点停止执行并等待任务完成。

使用 await 不会阻止调用方的线程, 而是将控件返回给调用方。 这意味着不会阻止调用线程, 因此, 例如, 在等待任务时不会阻止用户界面线程。

当任务完成时, 方法将继续在代码中的同一点执行。 这包括返回到 try-catch finally 块的 try 范围 (如果存在)。 await 不能用在 catch 或 finally 块中。

阅读有关[await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) Microsoft Docs 的详细信息。

## <a name="exception-handling"></a>异常处理

异步方法中发生的异常存储在任务中, 并在任务为`await`ed 时引发。 可以在 try catch 块中捕获和处理这些异常。

## <a name="cancellation"></a>取消

需要很长时间才能完成的异步方法应支持取消操作。 通常, 按如下所示调用取消:

- 创建`CancellationTokenSource`对象。
- `CancellationTokenSource.Token`实例被传递给可取消异步方法。
- 通过调用`CancellationTokenSource.Cancel`方法来请求取消。

然后, 该任务会自行取消并确认取消。

有关取消的详细信息，请参阅[微调异步应用程序 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)。

## <a name="example"></a>示例

下载[示例 Xamarin 解决方案](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)(适用于 iOS 和 Android), 以查看移动应用中`async`和`await`的工作示例。 本部分将对该示例代码进行更详细的讨论。

### <a name="writing-an-async-method"></a>编写 async 方法

下面的方法演示如何`async` `await`使用 ed 任务对方法进行编码:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

请注意以下几点:

- 方法声明包含`async`关键字。
- 返回类型为`Task<int>` , 因此调用代码可以`int`访问在此方法中计算的值。
- 返回语句`return exampleInt;`是一个整数对象-该方法返回`Task<int>`的事实是语言改进的一部分。


### <a name="calling-an-async-method-1"></a>调用 async 方法1

此按钮单击 "事件处理程序" 可在 Android 示例应用程序中找到, 以调用上述方法:

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

注意：

- 匿名委托具有 async 关键字前缀。
- 异步方法 DownloadHomepage 返回存储在 sizeTask\<变量中的任务 int >。
- 代码等待 sizeTask 变量。  *这*是方法挂起的位置, 并将控制权返回给调用代码, 直到异步任务在自己的线程上完成。
- 当在方法的第一行上创建任务时, 不管在该处创建任务, 执行不会暂停。 Await 关键字表示执行暂停的位置。
- 异步任务完成后, 将从 await 行中设置 intResult, 并在原始线程上继续执行。


### <a name="calling-an-async-method-2"></a>调用 async 方法2

在 iOS 示例应用程序中, 示例的编写方式略有不同, 以说明替代方法。 此示例不使用匿名委托, 而是声明`async`分配的事件处理程序, 如常规事件处理程序:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

然后定义事件处理程序方法, 如下所示:

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

一些重要事项:

- 方法标记为, `async`但返回。 `void` 通常仅对事件处理程序执行此操作 (否则, 你将`Task`返回`Task<TResult>`或)。
- 与前面`await`的示例不同`DownloadHomepage` , 在对变量 ( `intResult` ) 的赋值上, 直接在该方法上使用的代码用于`Task<int>`引用该任务。  *这*是将控件返回给调用方的位置, 直到异步方法在另一个线程上完成。
- 当异步方法完成并返回时, 执行将`await`继续, 这意味着返回整数结果, 然后在 UI 小组件中呈现。


## <a name="summary"></a>总结

使用 async 和 await 极大地简化了在后台线程上生成长时间运行的操作而不会阻止主线程所需的代码。 它们还可以在任务完成时轻松访问结果。

本文档提供了有关 Xamarin 和 Xamarin 的新语言关键字和示例的概述。



## <a name="related-links"></a>相关链接

- [AsyncAwait (示例)](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)
- [作为代的 "跳到" 语句的回调](https://tirania.org/blog/archive/2013/Aug-15.html)
- [数据 (iOS) (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/data/)
- [HttpClient (iOS) (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch (iOS) (示例)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [异步编程](https://docs.microsoft.com/dotnet/csharp/async)
- [微调异步应用程序 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await 和 UI, 以及死锁!哦!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [处理完成的任务](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [基于任务的异步模式 (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [异步 in C# 5 (Eric Lippert 的博客)-介绍关键字](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
