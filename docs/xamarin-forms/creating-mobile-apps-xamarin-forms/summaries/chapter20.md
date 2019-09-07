---
title: 第 20 章的摘要。 异步和文件 I/O
description: 用 Xamarin 创建移动应用程序：第 20 章的摘要。 异步和文件 I/O
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771038"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第 20 章的摘要。 异步和文件 I/O

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

 图形用户界面必须按顺序响应用户输入事件。 这意味着用户输入事件的所有处理必须都出现在单个线程中，通常称为*主线程*或*UI 线程*。

用户期望能够响应的图形用户界面。 这意味着程序必须快速处理用户输入事件。 如果这不可能，然后处理必须是要转移到辅助执行线程。

本书中的几个示例程序使用过[ `WebRequest` ](xref:System.Net.WebRequest)类。 此类中[ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object))方法启动工作线程，完成时调用回调函数。 但是，该回调函数在运行工作线程，因此，程序必须调用[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法访问的用户界面。

> [!NOTE]
> Xamarin.Forms 程序应使用[ `HttpClient` ](xref:System.Net.Http.HttpClient)而非[ `WebRequest` ](xref:System.Net.WebRequest)用于通过 internet 访问的文件。 `HttpClient` 支持异步操作。

异步处理到更为现代的方法是在.NET 和 C# 中可用。 这涉及[ `Task` ](xref:System.Threading.Tasks.Task)并[ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1)类和中的其他类型[ `System.Threading` ](xref:System.Threading)和[ `System.Threading.Tasks` ](xref:System.Threading.Tasks)命名空间，并将C#5.0`async`并`await`关键字。 这就是这一章的重点。

## <a name="from-callbacks-to-await"></a>从回调以等待

`Page`类本身包含三个异步方法，以显示警报框：

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 返回`Task`对象
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) 返回`Task<bool>`对象
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) 返回`Task<string>`对象

`Task`对象指示这些方法实现基于任务的异步模式，称为点击。 这些`Task`快速从方法返回的对象。 `Task<T>`返回的值构成"承诺"的类型的值`TResult`在任务完成时将提供。 `Task`返回值表示的异步操作将会完成，但不是带有任何值返回。

在所有这些情况下，`Task`时在用户消除警告框即告完成。  

### <a name="an-alert-with-callbacks"></a>使用回调警报

[ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)示例演示如何处理`Task<bool>`返回的对象和`Device.BeginInvokeOnMainThread`使用回调方法的调用。

### <a name="an-alert-with-lambdas"></a>与 lambda 警报

[ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)示例演示如何使用匿名 lambda 函数用于处理`Task`和`Device.BeginInvokeOnMainThread`调用。  

### <a name="an-alert-with-await"></a>使用 await 警报

更简单明了的方法涉及`async`和`await`关键字在 C# 5 中引入的。 [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)示例演示其用法。

### <a name="an-alert-with-nothing"></a>没有警报

如果异步方法返回`Task`而非`Task<TResult>`，则该程序不需要使用这些方法之一，如果它不需要知道在异步任务完成时。 [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)示例演示了这。

### <a name="saving-program-settings-asynchronously"></a>正在保存程序设置以异步方式

[ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)示例演示如何使用[ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法的`Application`要保存这些更改而无需计划设置重写`OnSleep`方法。

### <a name="a-platform-independent-timer"></a>独立于平台的计时器

可以使用[ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32))创建独立于平台的计时器。 [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)示例演示了这。

## <a name="file-inputoutput"></a>文件输入/输出

一直以来，.NET [ `System.IO` ](xref:System.IO)命名空间已被文件 I/O 支持的源。 虽然此命名空间中的某些方法支持异步操作，但大多数不适用。 命名空间还支持执行复杂的文件 I/O 功能的多个简单的方法调用。

### <a name="good-news-and-bad-news"></a>好消息和坏消息

受支持的 Xamarin.Forms 支持应用程序本地存储的所有平台&mdash;是私有的应用程序的存储。

Xamarin.iOS 和 Xamarin.Android 库包括 Xamarin 明确针对这两个平台的.NET 的版本。 其中包括来自类`System.IO`可用于在这两个平台中执行与应用程序本地存储的文件 I/O。

但是，如果搜索这些`System.IO`Xamarin.Forms PCL 中的类，您不会找到它们。 问题在于 Windows 运行时 API，Microsoft 彻底改观文件 I/O。 面向 Windows 8.1、 Windows Phone 8.1 和通用 Windows 平台的程序不使用`System.IO`用于文件 I/O。

这意味着，您将需要使用[ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (中首先讨论[**第 9 章。特定于平台的 API 调用**](chapter09.md)实现文件 I/O。

> [!NOTE]
> 使用.NET Standard 2.0 库，已替换为可移植类库和.NET Standard 2.0 支持[ `System.IO` ](xref:System.IO)所有 Xamarin.Forms 平台的类型。 不再需要使用`DependencyService`对于大部分文件 I/O 任务。 请参阅[Xamarin.Forms 中的文件处理](~/xamarin-forms/data-cloud/data/files.md)文件 I/O 使用更现代方法。

### <a name="a-first-shot-at-cross-platform-file-io"></a>第一个尝试跨平台文件 I/O

[ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)示例定义[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)界面，用于文件 I/O 和此接口中的所有平台的实现。 但是，因为 Windows 运行时文件 I/O 方法是异步 Windows 运行时实现不使用此接口中的方法。

### <a name="accommodating-windows-runtime-file-io"></a>适应 Windows 运行时文件 I/O

在 Windows 运行时下运行的程序使用中的类[ `Windows.Storage` ](/uwp/api/Windows.Storage)并[ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams)文件 I/O，包括本地存储应用程序的命名空间。 Microsoft 已确定要求超过 50 毫秒异步模式，以避免阻止 UI 线程的任何操作，因为这些文件 I/O 方法主要是异步的。

演示这种新方法的代码将在库中，以便可以由其他应用程序使用它。

## <a name="platform-specific-libraries"></a>特定于平台的库

最好在库中存储的可重用代码。 对于完全不同的操作系统的可重用代码的不同部分时，这是很明显更困难。

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案演示了一种方法。 此解决方案包含七个不同的项目：

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，正常的 Xamarin.Forms PCL
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)，iOS 类库
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，Android 类库
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，通用 Windows 类库
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，普遍适用于所有 Windows 平台的代码的共享的项目

所有单个平台项目 (除**Xamarin.FormsBook.Platform.WinRT**) 引用了**Xamarin.FormsBook.Platform**。 三个 Windows 项目具有对的引用**Xamarin.FormsBook.Platform.WinRT**。

所有项目都包含一个静态`Toolkit.Init`方法，以确保如果它不能直接引用 Xamarin.Forms 应用程序解决方案中的项目加载库。

**Xamarin.FormsBook.Platform**项目包含新[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)接口。 所有方法现在都已具有名称`Async`后缀并返回`Task`对象。

**Xamarin.FormsBook.Platform.WinRT**项目包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)的 Windows 运行时类。

**Xamarin.FormsBook.Platform.iOS**项目包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)适用于 iOS 的类。 这些方法现在必须是异步的。 某些方法使用异步版本中定义的方法`StreamWriter`并`StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String))并[ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync)。 其他人将转换为结果`Task`对象使用[ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*)方法。

**Xamarin.FormsBook.Platform.Android**项目包含一个类似[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)适用于 Android 的类。

**Xamarin.FormsBook.Platform**项目还包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)类可方便地使用`DependencyService`对象。

若要使用这些库，应用程序解决方案必须包括中的所有项目**Xamarin.FormsBook.Platform**解决方案，每个应用程序项目必须具有对中的相应库的引用**Xamarin.FormsBook.Platform**。

[ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解决方案演示了如何使用**Xamarin.FormsBook.Platform**库。 每个项目都调用`Toolkit.Init`。 应用程序将使用的异步文件 I/O 操作的函数。

### <a name="keeping-it-in-the-background"></a>使其保持背景

对多个异步方法进行调用的库中的方法&mdash;如`WriteFileAsync`并`ReadFileASync`Windows 运行时中的方法[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)类&mdash;可以进行某种程度上通过使用更高效[ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean))方法可避免切换回用户界面线程。

### <a name="dont-block-the-ui-thread"></a>不会阻止 UI 线程 ！

有时很容易避免使用`ContinueWith`或`await`通过使用[ `Result` ](xref:System.Threading.Tasks.Task`1.Result)方法的属性。 它可以阻止 UI 线程或甚至会挂起应用程序应避免此操作。

## <a name="your-own-awaitable-methods"></a>你自己的可等待方法

你可以通过将其传递到其中一个以异步方式运行某些代码[ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action))方法。 您可以调用`Task.Run`中的异步方法，会处理一些开销。

各种`Task.Run`模式将在下文。

### <a name="the-basic-mandelbrot-set"></a>基本的 Mandelbrot 集

若要绘制 mandelbrot 集合在真实时间中设置[ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库有[ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)结构类似于中的一个`System.Numerics`命名空间。

[ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)示例有`CalculateMandeblotAsync`计算基本的黑白的 Mandelbrot 集，并将其代码隐藏文件中的方法[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)以将其放在位图上。

### <a name="marking-progress"></a>将标记进度

报告进度，从异步方法，可以实例化[ `Progress<T>` ](xref:System.Progress`1)类，并定义在异步方法，以使类型的自变量[ `IProgress<T>` ](xref:System.IProgress`1)。 了这一点[ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)示例。

### <a name="cancelling-the-job"></a>正在取消作业

此外可以编写异步方法不可取消。 以名为的类开头[ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource)。 [ `Token` ](xref:System.Threading.CancellationTokenSource.Token)属性是类型的值[ `CancellationToken` ](xref:System.Threading.CancellationToken)。 这将传递到异步函数。 程序调用[ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel)方法的`CancellationTokenSource`（通常以响应用户操作） 以取消异步函数。

异步方法可以定期检查[ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested)的属性`CancellationToken`并退出此属性是否`true`，或只需调用[ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested)方法，请在这种情况下方法结尾[ `OperationCancelledException` ](xref:System.OperationCanceledException)。

[ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)示例演示如何使用可取消函数。

### <a name="an-mvvm-mandelbrot"></a>MVVM mandelbrot 集合

[ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)示例具有更广泛的用户界面，并且它主要基于[ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)并[ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)类：

[![Mandelbrot 集合 X F 的三个屏幕截图](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM mandelbrot 集合")

## <a name="back-to-the-web"></a>返回到 web

[ `WebRequest` ](xref:System.Net.WebRequest)某些示例中使用的类使用旧式异步协议调用的异步编程模型或 APM。 可以将此类转换为使用其中一个的现代点击协议`FromAsync`中的方法[ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1)类。 [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)示例演示了这。

## <a name="related-links"></a>相关链接

- [第 20 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第 20 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [使用文件](~/xamarin-forms/data-cloud/data/files.md)
