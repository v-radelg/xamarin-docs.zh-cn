---
title: 异步支持概述
description: 本文档介绍如何编程使用 async 和 await 中, 引入的概念C#第 5 步，使其更轻松地编写异步代码。
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0a72dead1b6c001f1514f1a089df9b407eb90644
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037259"
---
# <a name="async-support-overview"></a>异步支持概述

_C#5 引入了两个关键字来简化异步编程： async 和 await。这些关键字可以编写另一个线程中使用任务并行库执行长时间运行操作 （如网络访问权限） 的简单代码，并轻松地访问上完成的结果。最新版本的 Xamarin.iOS 和 Xamarin.Android 支持 async 和 await-本文档提供了说明和通过 Xamarin 使用新语法的示例。_

Xamarin 的异步支持的 Mono 3.0 基础上构建，并会将从正在 Silverlight 适合移动应用版本的.NET 4.5 的适合移动应用版本升级的 API 配置文件。

## <a name="overview"></a>概述

本文档引入了新的 async 和 await 关键字则演练一些简单的示例在 Xamarin.iOS 和 Xamarin.Android 中实现异步方法。

有关更完整的新异步功能的讨论C#5 （包括许多示例和不同的使用方案），请参阅 MSDN 文档[使用 Async 和 Await 的异步编程](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)。

示例应用程序发出简单的异步 web 请求 （而不阻塞主线程），然后使用下载的 html 和字符计数更新 UI。

 [![](async-images/AsyncAwait_427x368.png "示例应用程序而不会阻止主线程发出简单的异步 web 请求，然后使用下载的 html 和字符计数更新 UI")](async-images/AsyncAwait.png#lightbox)

Xamarin 的异步支持的 Mono 3.0 基础上构建，并会将从正在 Silverlight 适合移动应用版本的.NET 4.5 的适合移动应用版本升级的 API 配置文件。

## <a name="requirements"></a>要求

C#5 个功能要求 Xamarin.iOS 6.4 和 Xamarin.Android 4.8 中包含的 Mono 3.0。 系统将提示您升级在 Mono、 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 以充分利用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async` 并`await`新C#结合使用任务并行库来轻松地编写线程的代码，而不会阻止你的应用程序的主线程中执行长时间运行的任务的语言功能。

## <a name="async"></a>async

### <a name="declaration"></a>声明

`async`关键字放在方法声明中 （或在 lambda 或匿名方法） 以指示它包含可以以异步方式运行的代码 ie。 不会阻止调用方的线程。

方法标记为`async`应包含至少一个 await 表达式或语句。 如果没有`await`s 是在方法中存在，则将以同步方式运行 (相同就好像没有`async`修饰符)。 这也将导致编译器警告 （但不是错误）。

### <a name="return-types"></a>返回类型

异步方法应返回`Task`，`Task<TResult>`或`void`。

指定`Task`返回类型，如果该方法不返回任何其他值。

指定`Task<TResult>`如果该方法需要返回一个值，其中`TResult`是要返回的类型 (如`int`，例如)。

`void`返回类型主要用于事件处理程序需要它。 调用返回 void 的异步方法的代码不能`await`结果。

### <a name="parameters"></a>参数

异步方法不能声明`ref`或`out`参数。

## <a name="await"></a>await

Await 运算符可应用于一个方法标记为异步任务。 它会导致要停止在该点执行并等待，直到任务完成的方法。

使用 await 不会阻止调用方的线程-而是将控制权返回给调用方。 这意味着，不阻止调用线程，因此示例用户界面线程不会阻止等待任务时。

完成任务后，该方法恢复执行在代码中的相同位置。 这包括返回到 try – catch – finally 块 try 作用域 （如果存在）。 await 不能用在 catch 或 finally 块中。

详细了解[MSDN 上的 await](https://msdn.microsoft.com/library/vstudio/hh156528.aspx)。

## <a name="exception-handling"></a>异常处理

在异步方法中发生的异常存储在任务中，该任务时引发`await`ed。 这些异常捕获和处理 try catch 块内。

## <a name="cancellation"></a>取消

需要很长时间才能完成的异步方法应支持取消操作。 通常情况下，取消调用，如下所示：

- 一个`CancellationTokenSource`创建对象。
- `CancellationTokenSource.Token`实例传递给可取消的异步方法。
- 通过调用请求取消`CancellationTokenSource.Cancel`方法。

然后，该任务取消了自身，并确认取消。

有关取消的详细信息，请参阅[如何取消异步任务](https://msdn.microsoft.com/library/vstudio/jj155761.aspx)MSDN 上。

## <a name="example"></a>示例

下载[示例中的 Xamarin 解决方案](https://developer.xamarin.com/samples/mobile/AsyncAwait/)（适用于 iOS 和 Android） 若要查看的工作示例`async`和`await`移动应用中。 在本部分中的更详细地讨论的示例代码。

### <a name="writing-an-async-method"></a>编写异步方法

以下方法演示了如何编码`async`方法替换`await`ed 任务：

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

请注意以下几点：

-  方法声明`async`关键字。
-  返回类型是`Task<int>`以便调用代码可以访问`int`中此方法计算的值。
-  Return 语句是`return exampleInt;`这是一个整数对象 – 该方法返回事实`Task<int>`是语言改进的一部分。


### <a name="calling-an-async-method-1"></a>调用异步方法 1

此按钮单击事件处理程序可以找到 Android 示例应用程序以调用上文所述的方法中：

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

-  匿名委托具有 async 关键字前缀。
-  异步方法 DownloadHomepage 返回 Task <int> sizeTask 变量中存储。
-  该代码等待 sizeTask 变量上。  *这*是将方法挂起，且控件返回到调用代码在其自己的线程上异步任务完成之前的位置。
-  执行*不*暂停的方法，尽管存在正在创建的任务的第一行上创建任务时。 Await 关键字表示执行会暂停的位置。
-  异步任务完成时，设置 intResult 并从 await 行在原始线程上继续执行。


### <a name="calling-an-async-method-2"></a>调用异步方法 2

在 iOS 示例应用程序示例写入稍有不同演示一种替代方法。 而是使用一个匿名委托，它比本示例声明`async`等常规事件处理程序分配的事件处理程序：

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

如下所示，然后定义事件处理程序方法：

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

一些重要事项：

-  该方法标记为`async`但返回`void`。 这通常只是事件处理程序 (否则将返回`Task`或`Task<TResult>`)。
-  代码`await`上的 s`DownloadHomepage`直接上向变量赋值的方法 ( `intResult` ) 与前面的示例，我们使用中间不同`Task<int>`变量来引用该任务。  *这*是其中控制返回到调用方异步方法完成之前在另一个线程的位置。
-  在恢复执行时异步方法完成，并返回，`await`这意味着整数结果是返回，然后再呈现 UI 小组件中。


## <a name="summary"></a>总结

使用 async 和 await 极大地简化了生成而不会阻止主线程的后台线程上的长时间运行操作所需的代码。 它们还更加轻松地完成任务后访问结果。

本文档提供新语言关键字和示例的概述 Xamarin.iOS 和 Xamarin.Android。



## <a name="related-links"></a>相关链接

- [AsyncAwait （示例）](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [作为我们代的回调转到语句](https://tirania.org/blog/archive/2013/Aug-15.html)
- [数据 (iOS) （示例）](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) （示例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) （示例）](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [网络研讨会：C#异步在 iOS 和 Android （视频）](http://xamarin.wistia.com/medias/k27mc627xz)
- [异步编程使用 Async 和 Await (MSDN)](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)
- [微调异步应用程序 (MSDN)](https://msdn.microsoft.com/library/vstudio/jj155761.aspx)
- [Await、 UI 和死锁和 ！哦我 ！(MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [处理任务，因为它们完成 (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [基于任务的异步模式 (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [在中的实现异步C#5 （Eric Lippert 的博客） – 有关的关键字引入](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
