---
title: 第 5 部分 - 实际代码共享策略
description: 本文档介绍了实际代码共享方案，例如数据库、 文件访问、 网络操作和异步代码的策略。
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 2ad576f10fc0af5d96396d90b3e502e21da1182d
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728234"
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5 部分 - 实际代码共享策略

本部分提供有关如何共享代码的常见应用程序方案的示例。

## <a name="data-layer"></a>数据层

数据层由存储引擎和方法来读取和写入信息构成。 出于性能、 灵活性和跨平台兼容性 SQLite 数据库引擎被建议 Xamarin 跨平台应用程序。
它在各种平台包括 Windows、 Android、 iOS 和 mac 上运行

### <a name="sqlite"></a>SQLite

SQLite 是开放源代码数据库实现。 可以在[SQLite.org](https://www.sqlite.org/)中找到源和文档。SQLite 支持适用于每个移动平台：

- **iOS** – 内置于操作系统。
- **Android** – 内置于操作系统自 Android 2.2 (API 级别 10)。
- **Windows** – 请参阅[适用于通用 Windows 平台扩展的 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。

即使使用数据库引擎在所有平台上，来访问数据库的本机方法是不同的。 这两种 iOS 和 Android 提供内置的 Api 从 Xamarin.iOS 或 Xamarin.Android 访问 SQLite 可供使用，但是使用本机 SDK 方法提供不能共享 （而不是可能的 SQL 查询本身，假定它们以字符串形式存储） 的代码. 有关本机数据库功能搜索的详细信息`CoreData`在 iOS 或 Android 的`SQLiteOpenHelper`类; 因为这些选项不是跨平台它们不在本文的讨论范围。

### <a name="adonet"></a>ADO.NET

Xamarin.iOS 和 Xamarin.Android 的支持`System.Data`并`Mono.Data.Sqlite`(请参阅 Xamarin.iOS[文档](~/ios/data-cloud/system.data.md)的详细信息)。
使用这些命名空间，可编写适用于这两个平台的 ADO.NET 代码。 编辑项目的引用，包括`System.Data.dll`和`Mono.Data.Sqlite.dll`并添加以下 using 语句至代码：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

将工作，下面的示例代码：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

很明显会跨不同的方法和类 （此示例中是仅用于演示目的） 拆分 ADO.NET 的实际实现。

### <a name="sqlite-net--cross-platform-orm"></a>SQLite NET – 跨平台 ORM

ORM （或对象关系映射程序） 尝试简化在类中建模的数据的存储。 而不是该表创建或选择手动编写 SQL 查询，手动从提取的插入和删除数据类的字段和属性，ORM 额外提供一层会为你的代码。 使用反射来检查您的类结构，ORM 可以自动创建表和列匹配的一个类并生成查询，以读取和写入数据。 这允许应用程序代码，只需发送和检索到的 ORM，负责实质上的所有 SQL 操作的对象实例。

SQLite NET 充当一个简单的 ORM，您可以保存和检索您在 SQLite 中的类。 它会隐藏跨平台编译器指令和其他技巧相结合的 SQLite 访问的复杂性。

SQLite NET 功能：

- 通过将属性添加到模型的类定义的表。
- 数据库实例都由的子类`SQLiteConnection`，SQLite 的网络库中的主类。
- 数据可以插入、 查询和删除使用的对象。 任何 SQL 语句不是必需的 （尽管您可以编写 SQL 语句，如果需要）。
- 可以对返回的 SQLite NET 集合执行基本的 Linq 查询。

源代码和 SQLite NET 文档位于[github 上的 SQLite Net](https://github.com/praeclarum/sqlite-net)和已实现了这两个案例研究。 SQLite NET 代码的简单示例 (从*Tasky Pro*案例研究) 如下所示。

首先，`TodoItem`类使用属性来定义将字段设为数据库主密钥：

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

这允许`TodoItem`表上使用下面这行代码 （以及任何 SQL 语句） 创建`SQLiteConnection`实例：

```csharp
CreateTable<TodoItem> ();
```

表中的数据还与其他方法在操作`SQLiteConnection`（同样，无需进行的 SQL 语句）：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

请参阅完整的示例案例研究源代码。

## <a name="file-access"></a>文件访问

文件访问是确定以任何应用程序的关键部分。 可能不属于应用程序包含的文件的常见示例：

- SQLite 数据库文件。
- 用户生成 （文本、 图像、 声音、 视频） 的数据。
- 下载用于缓存 （映像、 html 或 PDF 文件） 的数据。

### <a name="systemio-direct-access"></a>System.IO 直接访问权限

Xamarin.iOS 和 Xamarin.Android 允许使用中的类的文件系统访问`System.IO`命名空间。

每个平台都有不同的访问限制，必须加以考虑：

- iOS 应用程序具有严格限制的文件系统访问权限的沙盒中运行。 Apple 进一步决定了如何通过指定会备份某些位置 （和其他人不是） 应使用文件系统。 请参阅[使用 Xamarin.iOS 中的文件系统](~/ios/app-fundamentals/file-system.md)更多详细信息的指南。
- Android 还将访问限制为某些目录中与应用程序，但它也支持外部媒体 （例如。 SD 卡） 和访问共享的数据。
- Windows Phone 8 (Silverlight) 不允许直接进行文件访问权限 – 仅可以使用操作文件`IsolatedStorage`。
- Windows 8.1 WinRT 和 Windows 10 UWP 项目中只提供通过异步文件操作`Windows.Storage`Api，这不同于其他平台。

#### <a name="example-for-ios-and-android"></a>适用于 iOS 和 Android 示例

将写入和读取文本文件的简单示例如下所示。
使用`Environment.GetFolderPath`允许运行 iOS 和 Android，其中的每个返回基于其文件系统约定是有效的目录上相同的代码。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

请参阅 Xamarin.iOS[使用文件系统](~/ios/app-fundamentals/file-system.md)文档的特定于 iOS 的文件系统功能的详细信息。 编写跨平台文件访问代码时，请记住某些文件系统区分大小写，并且具有不同的目录分隔符。 建议的文件名始终使用相同的大小写和`Path.Combine()`方法构造文件或目录路径时。

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>对于 Windows 8 和 Windows 10 的 Windows.Storage

在第20章
*用 Xamarin Book 创建移动应用* [book](https://developer.xamarin.com/r/xamarin-forms/book/) 。 [Async 和 File i/o](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)包括[Windows 8.1 和 Windows 10 的示例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)就可以读取和文件，使用支持的 Api 这些平台上的文件：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

请参阅[书籍章节](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)的更多详细信息。

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>在 Windows Phone 7 和 8 (Silverlight) 上的独立的存储

独立的存储来保存和加载文件跨所有 iOS、 Android 和较旧的 Windows Phone 平台是通用的 API。

它是默认的机制用于在已实现了 Xamarin.iOS 和 Xamarin.Android 以允许公共文件访问代码编写的 Windows Phone (Silverlight) 中的文件访问。 `System.IO.IsolatedStorage`类，可以在所有三个平台中引用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

请参阅[独立存储概述为 Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx)有关详细信息。

独立存储 Api 中不可用[的可移植类库](~/cross-platform/app-fundamentals/pcl.md)。 PCL 的一个替代方法是[PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>在 Pcl 中的跨平台文件访问

此外，还提供了与 PCL 兼容的 NuGet – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) ，可跨平台文件访问支持 Xamarin 的平台和最新的 Windows api。

## <a name="network-operations"></a>网络操作

大多数移动应用程序将具有网络组件，例如：

- 下载图像、 视频和音频 （例如。 缩略图、 照片、 音乐）。
- 下载文档 （例如。 HTML、 PDF)。
- 上传用户数据 （如照片或文本）。
- 访问 web 服务或第三方 Api （包括 SOAP、 XML 或 JSON）。

.NET Framework 提供几个不同的类，用于访问网络资源： `HttpClient`， `WebClient`，和`HttpWebRequest`。

### <a name="httpclient"></a>HttpClient

`HttpClient`类中`System.Net.Http`命名空间现已推出 Xamarin.iOS、 Xamarin.Android 和大多数 Windows 平台。 有一个[MICROSOFT HTTP 客户端库 NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) ，可用于将此 API 引入可移植类库（和 Windows Phone 8 Silverlight）。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient`类提供了一个简单的 API 来检索远程数据从远程服务器。

通用 Windows 平台运营*必须*是异步的即使 Xamarin.iOS 和 Xamarin.Android 支持同步操作 （这可以在后台线程上执行）。

对于简单的异步代码`WebClient`操作：

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` 还有`DownloadFileCompleted`和`DownloadFileAsync`来检索二进制数据。

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供了更多自定义比`WebClient`，因此需要更多代码以使用。

为简单的同步代码`HttpWebRequest`操作：

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

还有一个示例中的我们[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="Reachability" />

### <a name="reachability"></a>可访问性

在各种从快速的 Wi-fi 网络条件或 4g 连接到不佳的接待和慢速边缘数据链接下运行的移动设备。 因此，它是网络的很好的做法检测网络是否可用，因此，哪些类型是否可用，然后再尝试连接到远程服务器。

移动应用可能需要在这些情况下的操作包括：

- 如果网络不可用，告知用户。 如果它们已手动将其禁用 （例如。 飞行模式或关闭 Wi-fi） 然后它们可以解决此问题。
- 如果连接是 3g，应用程序的行为可能有所不同 （例如，Apple 不允许应用程序超过 20 Mb，要下载超过 3g）。 应用程序可以使用此信息来警告用户过多的下载时间检索大型文件时。
- 即使网络不可用，它是很好的做法启动其他请求之前验证与目标服务器的连接。 这将阻止应用的网络操作超时，重复，并且还允许信息更丰富的错误消息来向用户显示。

没有[Xamarin.iOS 示例](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample)可用 (后者基于 Apple[可访问性的示例代码](https://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html)) 以帮助检测网络可用性。

## <a name="webservices"></a>WebServices

请参阅我们的文档[使用 Web 服务](~/cross-platform/data-cloud/web-services/index.md)，其中涵盖访问其余部分中，使用 Xamarin.iOS 的 SOAP 和 WCF 终结点。 它可以手动创建 web 服务请求和分析响应，但也有可用于使这更简单，包括 Azure、 RestSharp 和 ServiceStack 库。 即使基本 WCF 操作可访问 Xamarin 应用程序中。

### <a name="azure"></a>Azure

Microsoft Azure 是一个云平台，提供各种适用于移动应用，包括数据存储和同步和推送通知服务。

请访问[azure.microsoft.com](https://azure.microsoft.com/)免费试用。

### <a name="restsharp"></a>RestSharp

RestSharp 是一个.NET 库，可以包括在移动应用程序提供简化了对 web 服务的访问的 REST 客户端。 它通过提供一个简单的 API 来请求数据和分析的 REST 响应来帮助。 RestSharp 很有用

[RestSharp 网站](http://restsharp.org/)包含[文档](https://github.com/restsharp/RestSharp/wiki)如何实现使用 RestSharp 的 REST 客户端。
RestSharp 上提供了 Xamarin.iOS 和 Xamarin.Android 示例[github](https://github.com/restsharp/RestSharp/)。

此外，还有 Xamarin.iOS 代码片段中的我们[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

ServiceStack 与 RestSharp，这两个服务器端解决方案来承载 web 服务，以及可以在移动应用程序访问这些服务中实现一个客户端库。

[ServiceStack 网站](http://servicestack.net/)文档和代码示例说明用途的项目和链接。 示例包括 web 服务，以及可以访问它的各种客户端应用程序的完整服务器端实现。

没有[Xamarin.iOS 示例](http://www.servicestack.net/monotouch/remote-info/)ServiceStack 网站中的代码片段上我们[Web Services 文档](~/cross-platform/data-cloud/web-services/index.md)。

### <a name="wcf"></a>WCF

Xamarin 工具可以帮助您使用一些 Windows Communication Foundation (WCF) 服务。 一般情况下，Xamarin 支持 WCF 附带了 Silverlight 运行时的相同客户端的子集。 这包括最常见编码和协议的 WCF 的实现： 文本编码的 SOAP 消息通过 HTTP 传输协议使用`BasicHttpBinding`。

由于的大小和 WCF 框架的复杂性，可能会超出由 Xamarin 的客户端子集域支持的作用域的当前和未来的服务实现。 此外，WCF 支持需要使用工具仅在 Windows 环境以生成代理中可用。

 <a name="Threading" />

## <a name="threading"></a>线程

应用程序响应能力非常重要的移动应用程序 – 用户期望应用程序能够加载和快速执行。 冻结屏幕将停止接受用户输入将出现以指示应用程序崩溃，所以一定不能阻塞 UI 线程长时间运行阻塞调用，例如网络请求或缓慢本地操作 （如解压缩文件）。 特别是在启动过程不应包含长时间运行的任务 – 所有移动平台将终止花很长时间加载的应用。

这意味着您的用户界面应实现进度指示器或快速显示，否则为可用 UI 和异步任务来执行后台操作。 执行后台任务需要的使用的线程，这意味着后台任务需要一种方法要发送回主线程以指示进度或工作已完成。

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>并行任务库

使用并行任务库创建的任务可以以异步方式运行，并返回其调用线程，使其非常适用于触发长时间运行的操作而不会阻止用户界面上。

简单的并行任务操作可能如下所示：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

该键`TaskScheduler.FromCurrentSynchronizationContext()`这将重复使用的调用该方法的线程 SynchronizationContext.Current (此处运行的主线程`MainThreadMethod`) 作为封送到该线程后的调用的方法。 这意味着如果在 UI 线程上调用方法时，它将运行`ContinueWith`回 UI 线程上的操作。

如果代码从其他线程开始任务，使用以下模式来创建对 UI 线程的引用，该任务可以仍对其进行调用：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>在 UI 线程上调用

对于不使用并行任务库的代码，每个平台都有其自己的语法的封送处理回 UI 线程的操作：

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin.Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS 和 Android 的语法要求的 'context' 类可用于这意味着该代码需要将此对象传递到需要在 UI 线程上的回调的任何方法。

若要共享代码中进行 UI 线程调用，请按照[IDispatchOnUIThread 示例](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net)(由[ @follesoe ](https://twitter.com/follesoe))。 声明和程序对`IDispatchOnUIThread`接口中的共享代码，然后实现特定于平台的类，如下所示：

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Xamarin.Forms 开发人员应使用[ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads)常见代码 （共享的项目或 PCL） 中。

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>平台和设备功能和性能降低

进一步处理不同的功能的具体示例给出了平台功能文档。 它可以处理检测不同的功能以及如何妥善降级应用程序提供良好的用户体验，即使应用程序不能运行，对其全部潜能。
