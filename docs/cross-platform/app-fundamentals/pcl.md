---
title: 可移植类库 (PCL) 简介
description: 本文介绍了可移植类库 (PCL) 项目，并将指导完成创建和使用适用于 Mac 和 Visual Studio 的 Visual Studio 中的 PCL 项目。
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a1e7152ca0f24d1ac660742e97907ea9300df9a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666874"
---
# <a name="portable-class-libraries-pcl"></a>可移植类库 (PCL)

> [!WARNING]
> 可移植类库 (Pcl) 被视为最新版本的 Visual Studio 中不推荐使用。
> 虽然仍可以打开、 编辑和编译 Pcl，为新项目建议使用[.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md)。

构建跨平台应用程序的关键组件能够在各种特定于平台的项目间共享代码。 但是，这被复杂，因为不同的平台通常使用另一子组的.NET 基类库 (BCL)，并因此实际上只会生成到不同的.NET Core 库配置文件。 这意味着，每个平台只能使用类库，因此它们将出现需要单独的类库项目的每个平台以相同的配置文件为目标。

有三种主要方法代码共享，解决此问题： **.NET Standard 项目**，**共享资产项目**，和**可移植类库 (PCL) 项目**.

- **.NET standard 项目**是首选的方法共享.NET 代码，详细了解[.NET Standard 项目和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)。
- **共享资产项目**使用一组文件和产品/服务的快速而简单的方法，用来共享代码的解决方案中，通常使用条件编译指令来指定将使用它 （有关详细信息的各种平台的代码路径信息，请参阅[共享的项目文章](~/cross-platform/app-fundamentals/shared-projects.md))。
- **PCL**项目以支持一组已知的 BCL 类/功能的特定配置文件为目标。 但是，为 PCL 缺点是，它们通常需要额外体系结构工作配置文件特定代码分离到他们自己的库。

此页说明如何创建**PCL**面向特定的配置文件，然后可由多个特定于平台的项目引用的项目。

## <a name="what-is-a-portable-class-library"></a>什么是可移植类库？

当您创建一个应用程序项目或类库项目时，生成的 DLL 被限制到为创建在特定平台上工作。 这可以防止您从编写 Windows 应用程序，程序集，然后将其重新使用 Xamarin.iOS 和 Xamarin.Android。

当您创建可移植类库时，但是，可以选择您希望代码上运行的平台的组合。 请创建可移植类库时的兼容性选项将转换为一个"配置文件"的标识符，其中描述了此库支持哪些平台。

下表显示了一些因.NET 平台的功能。 若要编写保证在特定的设备平台上运行的 PCL 程序集只需选择创建项目时需要哪些支持。

|功能|.NET Framework|UWP 应用|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|数据注释|4.0.3 +|Y|Y||Y|

Xamarin 列反映了这一事实，Xamarin.iOS 和 Xamarin.Android 支持随 Visual Studio 的所有配置文件，并且仅由你选择支持的其他平台受限于您创建的所有库中的功能可用性。

这包括的组合的配置文件：

- .NET 4 或.NET 4.5
- Silverlight 5
- Windows Phone 8
- UWP 应用

你可以阅读更多有关在不同的配置文件的功能[Microsoft 的网站](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)，并查看其他社区成员的[PCL 配置文件摘要](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)framework 信息以及其他说明，其中包括支持。

**权益**

1. 集中式的代码共享 – 中编写和测试代码可以使用由其他库或应用程序的单个项目。
2. 重构操作将影响所有代码加载到解决方案 （可移植类库和特定于平台的项目） 中。
3. PCL 项目中，可以轻松地引用其他项目在解决方案中，或可供其他人在其解决方案中引用共享输出程序集。

**缺点**

1. 因为多个应用程序之间共享相同的可移植类库时，不能 （例如引用特定于平台的库。 Community.CsharpSqlite.WP7).
2. 可移植类库子集可能包括应 MonoTouch 和 Mono for Android （如 DllImport 或 System.IO.File） 中提供的类。

> [!NOTE]
> 可移植类库中最新版本的 Visual Studio 中，已弃用并[.NET 标准库](net-standard.md)建议改为。

某种程度上这两个缺点可以绕过使用提供程序模式或依赖关系注入的代码在平台项目中针对可移植类库中定义的接口或基类的实际实现。

此图显示了使用可移植类库来共享代码，但它还使用依赖关系注入以传入依赖于平台的功能的跨平台应用程序的体系结构：

[![](pcl-images/image1.png "下图显示了使用可移植类库来共享代码，但它还使用依赖关系注入以传入依赖于平台的功能的跨平台应用程序的体系结构")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练

本部分逐步讲解如何创建和使用可移植类库使用 Visual Studio for mac。 请参阅完整的实现中的 PCL 示例部分。

### <a name="creating-a-pcl"></a>创建 PCL

将可移植类库添加到你的解决方案是非常类似于常规的库项目中添加。

1. 在中**新的项目**对话框中，选择**多平台 > 库 > 可移植库**选项：

    ![创建新的 PCL 项目](pcl-images/image2.png)

1. PCL 在 Visual Studio for Mac 创建时它是自动配置适用于 Xamarin.iOS 和 Xamarin.Android 的配置文件。 PCL 项目将显示此屏幕截图中所示：

    ![在 solution pad 中的 PCL 项目](pcl-images/image3.png)

在 PCL 中现已准备好要添加的代码。 此外可以引用其他项目 （应用程序项目，库项目和甚至是其他 PCL 项目）。

### <a name="editing-pcl-settings"></a>编辑 PCL 设置

若要查看和更改此项目的 PCL 设置，右键单击该项目并选择**选项 > 生成 > 常规**以查看屏幕如下所示：

[![若要设置配置文件的 PCL 项目选项](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

单击**更改...** 来改变此可移植类库的目标配置文件。

如果已向 PCL 添加代码后更改配置文件，，就可以在库将无法再编译，如果代码引用不是新选择了配置文件的一部分的功能。

## <a name="working-with-a-pcl"></a>使用 PCL

当 PCL 库中编写代码时，Visual Studio for Mac 编辑器将识别所选配置文件的限制，并相应地调整自动完成选项。 例如，此屏幕截图显示了 System.IO 的自动完成选项使用在 Visual Studio for Mac 中使用的默认配置文件 (Profile136) – 请注意滚动条，该值指示是否显示大约一半的可用类 （实际上有仅 14可用的类）。

[![智能感知列表 14 中的类 PCL 的 System.IO 类](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

进行比较，使用自动完成在 Xamarin.iOS 或 Xamarin.Android 项目中 – System.IO 有 40 类通常包括可使用的类，如`File`和`Directory`这不是任何 PCL 配置文件中。

[![.NET Framework System.IO 命名空间中的 40 类的智能感知列表](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

这反映了使用 PCL 的基础的权衡 – 能够跨多个平台无缝地共享代码意味着某些 Api 不可用于你，因为它们没有可比较实现跨所有可能的平台。

### <a name="using-pcl"></a>使用 PCL

一旦创建 PCL 项目后，你可以与您通常将引用添加相同的方式添加对它从任何兼容的应用程序或库项目的引用。 在 Visual Studio for Mac 中，右键单击引用节点并选择**编辑引用...** 然后切换到**项目**选项卡上所示：

[![添加对通过编辑引用选项 PCL 的引用](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

下面的屏幕截图显示了 Solution pad TaskyPortable 示例应用程序，在 Xamarin.iOS 项目显示在底部，对该 PCL 库的引用的 PCL 库。

[![TaskyPortable 示例解决方案显示 PCL 项目](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

PCL 的输出 (即。 生成的程序集 DLL) 也可以作为对大多数项目的引用添加。 这使得 PCL 提供跨平台组件和库的理想方法。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 演练

本部分将指导完成如何创建和使用可移植类库使用 Visual Studio。 请参阅完整的实现中的 PCL 示例部分。

### <a name="creating-a-pcl"></a>创建 PCL

将 PCL 添加到你在 Visual Studio 中的解决方案是添加常规项目略有不同：

1. 在中**添加新项目**屏幕上，选择**类库 （旧版可移植）** 选项。 请注意右侧说明建议此项目类型已被弃用。

    [![新项目窗口创建可移植类库](pcl-images/image10-sml.png "可移植类库")](pcl-images/image10.png#lightbox)

2. Visual Studio 会立即提示以下对话框，以便可以配置该配置文件。
 刻度线的平台需要支持，并按确定。

    [![选择库的目标平台](pcl-images/image11-sml.png "勾选您需要支持，并按确定的平台")](pcl-images/image11.png#lightbox)

3. PCL 项目将显示在解决方案资源管理器中所示&ndash;文本 **（可移植）** 项目名称，以指示它是一个 PCL 旁边将显示：

    ![NET Framework 定义的 PCL 配置文件](pcl-images/image12.png "NET Framework 定义的 PCL 配置文件")

在 PCL 中现已准备好要添加的代码。 此外可以引用其他项目 （应用程序项目，库项目和甚至是其他 PCL 项目）。

### <a name="editing-pcl-settings"></a>编辑 PCL 设置

PCL 设置都可以查看和更改通过右键单击项目，然后选择**属性 > 库**，此屏幕截图中所示：

[![编辑平台目标](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

如果已向 PCL 添加代码后更改配置文件，，就可以在库将无法再编译，如果代码引用不是新选择了配置文件的一部分的功能。

> [!TIP]
> 此外，还有一条消息提示的 **。NETStandard 是用于共享代码的建议的方法**。 这是指示，尽管 Pcl 中仍受支持，建议升级到.NET Standard。

### <a name="working-with-a-pcl"></a>使用 PCL

当 PCL 库中编写代码时，Visual Studio 将识别所选配置文件的限制，并相应地调整 Intellisense 选项。 例如，此屏幕截图显示了 System.IO 的自动完成选项使用默认配置文件 (Profile136) – 请注意滚动条，该值指示是否显示大约一半的可用类 （实际上是仅 14 类可用）。

[![减少的 IO 类在 PCL 中可用的数量](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

进行比较，使用常规的项目 – 中的自动完成 System.IO 有 40 类通常包括可使用的类，如`File`和`Directory`这不是任何 PCL 配置文件中。

[![很多更多 IO 的类在.NET Framework 中可用](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

这反映了使用 PCL 的基础的权衡 – 能够跨多个平台无缝地共享代码意味着某些 Api 不可用于你，因为它们没有可比较实现跨所有可能的平台。

> [!TIP]
> .NET standard 2.0 表示更大 API 图面上的区域比 Pcl，包括 System.IO 命名空间。 对于新项目，.NET 标准建议而不是 PCL。

### <a name="using-pcl"></a>使用 PCL

一旦创建 PCL 项目后，你可以与您通常将引用添加相同的方式添加对它从任何兼容的应用程序或库项目的引用。 在 Visual Studio 中，右键单击引用节点并选择`Add Reference...`然后切换到**解决方案 > 项目**选项卡上所示：

[![添加对通过添加引用项目选项卡的 PCL 的引用](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

下面的屏幕截图显示了 TaskyPortable 示例应用中，在 Xamarin.iOS 项目显示在底部，对该 PCL 库的引用的 PCL 库的解决方案窗格。

[![显示了 PCL 库 TaskyPortable 示例解决方案](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

PCL 的输出 (即。 生成的程序集 DLL) 也可以作为对大多数项目的引用添加。
这使得 PCL 提供跨平台组件和库的理想方法。

-----

## <a name="pcl-example"></a>PCL 示例

[TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/)示例应用程序演示了如何通过 Xamarin 使用可移植类库。
下面是生成 iOS 和 Android 上运行的应用的一些屏幕快照：

[![](pcl-images/image18.png "下面是生成的应用在 iOS、 Android 和 Windows Phone 上运行的一些屏幕快照")](pcl-images/image18.png#lightbox)

共享多个数据和逻辑是完全可移植代码的类，它还演示了如何结合使用依赖关系注入的 SQLite 数据库实现的特定于平台的要求。

解决方案结构如下所示 (在 Visual Studio for Mac 和 Visual Studio 中分别):

[![](pcl-images/image19.png "解决方案结构是此处分别显示为 Visual Studio for Mac 和 Visual Studio")](pcl-images/image19.png#lightbox)

由于 SQLite NET 代码有特定于平台的部分 （可使用 SQLite 实现每个不同的操作系统上），用于演示目的重构到一个抽象类，该类可编译为可移植类库和实际的代码实现为 iOS 和 Android 项目中的子类。

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

可移植类库可以支持的.NET 功能方面的限制。 因为它被编译多个平台上运行，但不能使用`[DllImport]`SQLite 的网络中使用的功能。 改为 SQLite NET 是一个抽象类作为实现，然后引用共享代码的其余部分中。 抽象 API 提取如下所示：

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

共享代码的其余部分使用抽象类"存储"和"检索"对象中的数据库。 在任何应用程序中，使用此抽象类，我们必须通过在完整的实现，提供实际的数据库功能。

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS

IOS 和 Android 应用程序项目包含用户界面和用来布置在 pcl 中共享的代码的其他特定于平台的代码。

这些项目还包含抽象数据库 API，适用于该平台的实现。 在 iOS 和 Android Sqlite 数据库引擎是内置于操作系统，因此可以使用实现`[DllImport]`提供数据库连接的具体实现，如上所示。 一段摘录的特定于平台的实现代码如下所示：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

完整的实现所示的示例代码。

## <a name="summary"></a>总结

本文简要讨论过的可移植类库的优缺点，演示了如何创建和使用从 Pcl 在 Visual Studio for Mac 和 Visual Studio;最后引入了完整的示例应用程序 – TaskyPortable – 显示操作中的 PCL 的和。

## <a name="related-links"></a>相关链接

- [TaskyPortable （示例）](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [使用.NET Framework (Microsoft) 的跨平台开发](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
