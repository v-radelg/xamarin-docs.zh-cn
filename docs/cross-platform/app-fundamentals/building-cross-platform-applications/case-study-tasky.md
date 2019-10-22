---
title: 跨平台应用案例研究： Tasky
description: 本文档介绍如何设计 Tasky 可移植示例应用程序并将其构建为跨平台的移动应用程序。 它讨论了应用的要求、接口、数据模型、核心功能、实现等。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 246ee002404fdf6fe1120c19701aceb3c2dee7db
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "71249776"
---
# <a name="cross-platform-app-case-study-tasky"></a>跨平台应用案例研究： Tasky

*Tasky* *便携*是一个简单的待办事项列表应用程序。 本文档介绍了如何根据[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档的指导，对其进行设计和构建。 讨论涵盖以下几个方面：

<a name="Design_Process" />

## <a name="design-process"></a>设计过程

在开始编码之前，最好为要实现的内容创建一个道路地图。 这对于跨平台开发尤其适用，在该开发中，你将构建将以多种方式公开的功能。 从一开始就可以清楚地了解你的构建内容，稍后会在开发周期中节省时间和精力。

 <a name="Requirements" />

### <a name="requirements"></a>要求

设计应用程序的第一步是确定所需的功能。 这些可能是高级目标或详细用例。 Tasky 具有直接的功能性要求：

- 查看任务列表
- 添加、编辑和删除任务
- 将任务状态设置为 "已完成"

你应考虑使用特定于平台的功能。  Tasky 是否可以利用 iOS 地理围栏或 Windows Phone 的活动磁贴？ 即使您在第一个版本中未使用特定于平台的功能，您也应该提前计划以确保您的业务 & 数据层可以容纳它们。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>用户界面设计

从可跨目标平台实现的高级设计着手。 请注意针对特定 UI 约束。 例如，iOS 中的 `TabBarController` 可以显示五个以上的按钮，而 Windows Phone 等效最多可显示四个按钮。
使用所选的工具（工作方式）绘制屏幕流。

 [![](case-study-tasky-images/taskydesign.png "Draw the screen-flow using the tool of your choice paper works")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>数据模型

了解需要存储哪些数据将有助于确定要使用的持久性机制。 有关可用存储机制的信息，请参阅[跨平台数据访问](~/cross-platform/app-fundamentals/index.md)，以及如何帮助进行确定。 对于此项目，我们将使用 SQLite.NET。

Tasky 需要为每个 "TaskItem" 存储三个属性：

- **Name** – String
- **说明**-字符串
- **完成**–布尔值

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

请考虑需要用户界面使用来满足要求的 API。 待办事项列表需要以下函数：

- **列出所有任务**-显示所有可用任务的主屏幕列表
- **获取一个任务**–接触到任务行
- **保存一个任务**–编辑任务时
- **删除一个任务**–删除任务时
- **创建空任务**–创建新任务时

若要实现代码重用，此 API 应在*可移植类库*中实现一次。

 <a name="Implementation" />

### <a name="implementation"></a>实现

在对应用程序设计达成共识后，请考虑如何将其实现为跨平台应用程序。 这将成为应用程序的体系结构。 按照[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档中的指导进行操作，应用程序代码应细分为以下部分：

- **通用代码**-包含用于存储任务数据的重复使用代码的常见项目;公开模型类和 API 以管理数据的保存和加载。
- **特定于平台的代码**–特定于平台的项目，这些项目实现每个操作系统的本机 UI，并利用通用代码作为 "后端"。

[![](case-study-tasky-images/taskypro-architecture.png "Platform-specific projects implement a native UI for each operating system, utilizing the common code as the back end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

以下各部分介绍了这两个部分。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>常见（PCL）代码

Tasky 便携使用可移植类库策略来共享通用代码。 请参阅[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)文档，了解代码共享选项的说明。

所有常见代码（包括数据访问层、数据库代码和协定）都放置在库项目中。

下面演示了完整的 PCL 项目。 可移植库中的所有代码都与每个目标平台兼容。 部署时，每个本机应用都将引用该库。

![](case-study-tasky-images/portable-project.png "When deployed, each native app will reference that library")

下图显示按层分组的类。 @No__t_0 类是来自 Sqlite 网络包的样板代码。 类的其余部分是 Tasky 的自定义代码。 @No__t_0 和 `TaskItem` 类表示向特定于平台的应用程序公开的 API。

 [![](case-study-tasky-images/classdiagram-core.png "The TaskItemManager and TaskItem classes represent the API that is exposed to the platform-specific applications")](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空间分隔层可帮助管理每个层之间的引用。 平台特定的项目应该只需要为业务层包含 `using` 语句。 数据访问层和数据层应由业务层中 `TaskItemManager` 公开的 API 进行封装。

 <a name="References" />

### <a name="references"></a>reference

可移植类库需要在多个平台上可用，每个平台都具有对平台和框架功能的不同级别的支持。 因此，可使用哪些包和框架库存在限制。 例如，Xamarin 不支持 c # `dynamic` 关键字，因此可移植类库不能使用依赖于动态代码的任何包，即使此类代码可在 Android 上运行。 Visual Studio for Mac 将会阻止你添加不兼容的包和引用，但你需要考虑一些限制，以免以后出现惊喜。

注意：你会看到你的项目引用了你未使用的框架库。 这些引用包含为 Xamarin 项目模板的一部分。 在编译应用程序时，链接过程会删除未引用的代码，因此即使已引用 `System.Xml`，它也不会包含在最终的应用程序中，因为我们不使用任何 Xml 函数。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>数据层（DL）

数据层包含对数据进行物理存储的代码-无论是数据库、平面文件还是其他机制。 Tasky 数据层由两部分组成： SQLite 网络库和添加到连接的自定义代码。

Tasky 依赖于 Sqlite 网络 nuget 包（由 Frank Kreuger 发布）来嵌入提供对象关系映射（ORM）数据库接口的 SQLite 网络代码。 @No__t_0 类继承自 `SQLiteConnection` 并添加了所需的创建、读取、更新、删除（CRUD）方法，以便将数据读取和写入 SQLite。 它是可在其他项目中重复使用的泛型 CRUD 方法的简单样板实现。

@No__t_0 是一种单独的，可确保对同一实例进行所有访问。 锁定用于阻止从多个线程进行并发访问。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>Windows Phone 上的 SQLite

尽管 iOS 和 Android 都附带了 SQLite 作为操作系统的一部分，但 Windows Phone 不包括兼容的数据库引擎。 若要在所有三个平台上共享代码，需要使用 Windows phone-SQLite 的本机版本。 有关为 Sqlite 设置 Windows Phone 项目的详细信息，请参阅使用[本地数据库](~/xamarin-forms/data-cloud/data/databases.md)。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用接口通用化数据访问

数据层依赖于 `BL.Contracts.IBusinessIdentity` 以便实现需要主键的抽象数据访问方法。 然后，任何实现接口的业务层类都可以保留在数据层中。

接口只需指定一个整数属性作为主键：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基类实现接口，并添加 SQLite-NET 特性以将其标记为自动递增的主键。 然后，业务层中实现此基类的任何类都可以保留在数据层中：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

使用接口的数据层中的泛型方法示例如下 `GetItem<T>` 方法：

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>锁定以防止并发访问

在 `TaskItemDatabase` 类中实现[锁定](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx)，以防止对数据库进行并发访问。 这是为了确保序列化来自不同线程的并发访问（否则，UI 组件可能会在后台线程正在更新该数据库时尝试读取该数据库）。 下面显示了如何实现该锁的示例：

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

可在其他项目中重复使用大部分数据层代码。 该层中唯一特定于应用程序的代码是 `TaskItemDatabase` 构造函数中的 `CreateTable<TaskItem>` 调用。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>数据访问层（DAL）

@No__t_0 类使用强类型 API 封装数据存储机制，该 API 允许创建、删除、检索和更新 `TaskItem` 对象。

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用条件编译

类使用条件编译来设置文件位置，这是实现平台分歧的示例。 返回路径的属性在每个平台上编译为不同的代码。 代码和特定于平台的编译器指令如下所示：

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

根据具体的平台，输出将为适用于 iOS 的 "<app
path>/Library/TaskDB.db3"、适用于 Android 的 "<app
path>/Documents/TaskDB.db3" 或 "TaskDB" （对于 Windows Phone 为 "db3"）。

### <a name="business-layer-bl"></a>业务层（BL）

业务层实现了模型类和用于管理它们的外观。
在 Tasky 中，模型是 `TaskItem` 类，`TaskItemManager` 实现外观模式来提供用于管理 `TaskItems` 的 API。

 <a name="Façade" />

#### <a name="faade"></a>表面

 `TaskItemManager` 包装 `DAL.TaskItemRepository`，以提供应用程序和 UI 层将引用的 Get、Save 和 Delete 方法。

如果需要，业务规则和逻辑将放在此处，例如在保存对象之前必须满足的任何验证规则。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>用于平台特定代码的 API

编写通用代码后，必须生成用户界面以收集和显示由其公开的数据。 @No__t_0 类实现了外观模式，为应用程序代码提供访问的简单 API。

在每个特定于平台的项目中编写的代码通常与该设备的本机 SDK 紧密耦合，只使用由 `TaskItemManager` 定义的 API 访问通用代码。 这包括它公开的方法和业务类，如 `TaskItem`。

映像不在平台之间共享，而是单独添加到每个项目。 这一点非常重要，因为每个平台以不同的文件名、目录和分辨率来处理映像。

其余部分讨论了 Tasky UI 的特定于平台的实现细节。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS 应用

使用通用 PCL 项目存储和检索数据时，只需使用少量的类即可实现 iOS Tasky 应用程序。 完整的 iOS Xamarin iOS 项目如下所示：

 ![](case-study-tasky-images/taskyios-solution.png "iOS project is shown here")

此关系图中显示了这些类，并将其分组到层中。

 [![](case-study-tasky-images/classdiagram-android.png "The classes are shown in this diagram, grouped into layers")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>reference

IOS 应用引用特定于平台的 SDK 库–例如： Xamarin 和 Monotouch.dialog。

它还必须引用 `TaskyPortableLibrary` PCL 项目。
引用列表如下所示：

 ![](case-study-tasky-images/taskyios-references.png "The references list is shown here")

应用程序层和用户界面层是在此项目中使用这些引用实现的。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层（AL）

应用程序层包含特定于平台的类，需要将 PCL 向 UI 公开的对象 "绑定"。 IOS 特定的应用程序有两个类来帮助显示任务：

- **EditingSource** –此类用于将任务列表绑定到用户界面。 由于 `MonoTouch.Dialog` 用于任务列表，因此我们需要实现此帮助程序以启用 `UITableView` 中的 "轻扫到删除" 功能。 刷删除在 iOS 上很常见，但不能在 Android 或 Windows Phone 上进行，因此，iOS 特定项目是实现它的唯一项目。
- **TaskDialog** –此类用于将单个任务绑定到 UI。 它使用 `MonoTouch.Dialog` 反射 API 来 "包装" `TaskItem` 对象，该类包含正确的特性，以允许正确设置输入屏幕的格式。

@No__t_0 类使用 `MonoTouch.Dialog` 属性基于类的属性创建屏幕。 该类如下所示：

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

请注意，`OnTap` 属性需要方法名称–这些方法必须存在于创建 `MonoTouch.Dialog.BindingContext` 的类中（在此示例中，将在下一节中讨论 `HomeScreen` 类）。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>用户界面层（UI）

用户界面层由以下类组成：

1. **AppDelegate** –包含对外观 API 的调用，以对应用程序中使用的字体和颜色进行样式。 Tasky 是一个简单的应用程序，因此 `FinishedLaunching` 不会运行其他初始化任务。
2. **屏幕**-定义每个屏幕及其行为的 `UIViewController` 的子类。 屏幕将 UI 与应用程序层类和通用 API （`TaskItemManager`）结合在一起。 在此示例中，屏幕是在代码中创建的，但它们可能是使用 Xcode 的 Interface Builder 或情节提要设计器设计的。
3. **图像**–可视元素是每个应用程序的重要组成部分。 Tasky 具有初始屏幕和图标图像，适用于 iOS 的情况必须按常规和 Retina 的分辨率提供。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

主屏幕是显示 SQLite 数据库中的任务列表的 `MonoTouch.Dialog` 屏幕。 它从 `DialogViewController` 继承，并实现代码以将 `Root` 设置为包含要显示的 `TaskItem` 对象的集合。

 [![](case-study-tasky-images/ios-taskylist.png "It inherits from DialogViewController and implements code to set the Root to contain a collection of TaskItem objects for display")](case-study-tasky-images/ios-taskylist.png#lightbox)

与显示任务列表和交互相关的两个主要方法是：

1. **PopulateTable** –使用业务层的 `TaskManager.GetTasks` 方法检索要显示的 `TaskItem` 对象的集合。
2. **选中**–当涉及某个行时，将在新屏幕中显示该任务。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

任务详细信息是允许编辑或删除任务的输入屏幕。

Tasky 使用 `MonoTouch.Dialog` 的反射 API 显示屏幕，因此没有 `UIViewController` 实现。 相反，`HomeScreen` 类使用应用程序层中的 `TaskDialog` 类实例化并显示 `DialogViewController`。

此屏幕截图显示了一个空屏幕，其中演示了在 "**名称**" 和 "**注释**" 字段中设置水印文本的 `Entry` 属性：

 [![](case-study-tasky-images/ios-taskydetail.png "This screenshot shows an empty screen that demonstrates the Entry attribute setting the watermark text in the Name and Notes fields")](case-study-tasky-images/ios-taskydetail.png#lightbox)

"**任务详细信息**" 屏幕（如保存或删除任务）的功能必须在 `HomeScreen` 类中实现，因为这是创建 `MonoTouch.Dialog.BindingContext` 的位置。 以下 `HomeScreen` 方法支持 "任务详细信息" 屏幕：

1. **ShowTaskDetails** –创建用于呈现屏幕的 `MonoTouch.Dialog.BindingContext`。 它使用反射创建输入屏幕，以检索 `TaskDialog` 类中的属性名称和类型。 附加信息（如输入框的水印文本）是通过属性上的属性实现的。
2. **SaveTask** –此方法通过 `OnTap` 属性在 `TaskDialog` 类中引用。 按下 "**保存**" 时将调用此方法，并使用 `MonoTouch.Dialog.BindingContext` 检索用户输入的数据，然后再使用 `TaskItemManager` 保存更改。
3. **DeleteTask** –此方法通过 `OnTap` 属性在 `TaskDialog` 类中引用。 它使用 `TaskItemManager` 来使用 primary key （ID 属性）删除数据。

 <a name="Android_App" />

## <a name="android-app"></a>Android 应用

完整的 Xamarin Android 项目如下图所示：

 ![](case-study-tasky-images/taskyandroid-solution.png "Android project is pictured here")

类图，具有按层分组的类：

 [![](case-study-tasky-images/classdiagram-android.png "The class diagram, with classes grouped by layer")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>reference

Android 应用程序项目必须引用特定于平台的 Xamarin 程序集才能访问 Android SDK 中的类。

它还必须引用 PCL 项目（例如 TaskyPortableLibrary）访问通用数据和业务层代码。

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary to access the common data and business layer code")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层（AL）

与前面所述的 iOS 版本类似，Android 版本中的应用程序层包含特定于平台的类，需要将核心公开的对象 "绑定" 到 UI。

 **TaskListAdapter** –若要显示对象的列表 \<T > 需要实现适配器以在 `ListView` 中显示自定义对象。 适配器控制列表中的每个项使用哪个布局-在这种情况下，代码使用 Android 内置布局 `SimpleListItemChecked`。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面（UI）

Android 应用的用户界面层是代码和 XML 标记的组合。

- **资源/布局**–以 main.axml 文件形式实现的屏幕布局和行单元格设计。 MAIN.AXML 可以手动编写，也可以使用适用于 Android 的 Xamarin UI 设计器以可视方式进行布局。
- **资源/** 图形–图像（图标）和自定义按钮。
- **屏幕**-定义每个屏幕及其行为的活动子类。 将 UI 与应用程序层类和通用 API （`TaskItemManager`）结合在一起。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

主屏幕包含一个活动子类 `HomeScreen` 和定义布局（按钮和任务列表的位置）的 `HomeScreen.axml` 文件。 屏幕如下所示：

 [![](case-study-tasky-images/android-taskylist.png "The screen looks like this")](case-study-tasky-images/android-taskylist.png#lightbox)

主屏幕代码定义用于单击按钮并单击列表中的项的处理程序，并在 `OnResume` 方法中填充列表（以便反映在 "任务详细信息" 屏幕中所做的更改）。 数据使用业务层的 `TaskItemManager` 和应用程序层的 `TaskListAdapter` 进行加载。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

"任务详细信息" 屏幕还包括一个 `Activity` 子类和一个 MAIN.AXML 布局文件。 布局确定输入控件的位置， C#类定义加载和保存 `TaskItem` 对象的行为。

 [![](case-study-tasky-images/android-taskydetail.png "The class defines the behavior to load and save TaskItem objects")](case-study-tasky-images/android-taskydetail.png#lightbox)

对 PCL 库的所有引用通过 `TaskItemManager` 类进行。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone 应用
完整的 Windows Phone 项目：

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone App The complete Windows Phone project")

下图显示了分组为层的类：

 [![](case-study-tasky-images/classdiagram-wp7.png "This diagram presents the classes grouped into layers")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>reference

特定于平台的项目必须引用所需的特定于平台的库（如 `Microsoft.Phone` 和 `System.Windows`），以创建有效的 Windows Phone 应用程序。

它还必须引用 PCL 项目（例如 `TaskyPortableLibrary`）来利用 `TaskItem` 类和数据库。

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary to utilize the TaskItem class and database")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层（AL）

同样，对于 iOS 和 Android 版本，应用程序层还包含可帮助将数据绑定到用户界面的非可视元素。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

Viewmodel 将数据从 PCL （`TaskItemManager`）中换行，并以可以由 Silverlight/XAML 数据绑定使用的方式进行呈现。 这是特定于平台的行为的示例（如跨平台应用程序文档中所述）。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面（UI）

XAML 具有唯一的数据绑定功能，该功能可在标记中声明，并减少显示对象所需的代码量：

1. **Pages** – XAML 文件及其代码隐藏文件定义用户界面并引用 VIEWMODEL 和 PCL 项目以显示和收集数据。
2. **映像**–初始屏幕、背景和图标图像是用户界面的关键部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 类使用 `TaskListViewModel` 通过 XAML 的数据绑定功能来显示数据。 页面的 `DataContext` 设置为异步填充的视图模型。 XAML 中的 `{Binding}` 语法确定数据的显示方式。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

通过将 `TaskViewModel` 绑定到 TaskDetailsPage 中定义的 XAML 来显示每个任务。 通过业务层中的 `TaskItemManager` 检索任务数据。

 <a name="Results" />

## <a name="results"></a>结果

生成的应用程序在每个平台上的外观如下：

 <a name="iOS" />

### <a name="ios"></a>iOS

应用程序使用 iOS 标准用户界面设计，如定位在导航栏中的 "添加" 按钮和使用内置**加号（+）** 图标。 它还使用默认 `UINavigationController` "后退" 按钮行为，并支持表中的 "轻扫到删除"。

 [![](case-study-tasky-images/ios-taskylist.png "它还使用默认的 UINavigationController "后退" 按钮行为，并支持表中的 "轻扫删除"")](case-study-tasky-images/ios-taskylist.png#lightbox)[![](case-study-tasky-images/ios-taskylist.png "它还使用默认的 UINavigationController "后退" 按钮行为，并支持表中的 "轻扫删除"")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

### <a name="android"></a>Android

Android 应用使用内置控件，包括需要显示 "刻度" 的行的内置布局。 除了屏幕上的 "后退" 按钮外，还支持硬件/系统返回行为。

 [![](case-study-tasky-images/android-taskylist.png "The hardware/system back behavior is supported in addition to an on-screen back button")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "The hardware/system back behavior is supported in addition to an on-screen back button")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

### <a name="windows-phone"></a>Windows Phone

Windows Phone 应用使用标准布局，在屏幕底部填充应用栏，而不是在顶部显示导航栏。

 [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 应用使用标准布局，在屏幕底部填充应用栏，而不是在顶部显示导航栏")](case-study-tasky-images/wp-taskylist.png#lightbox)[![](case-study-tasky-images/wp-taskylist.png "Windows Phone 应用使用标准布局，在屏幕底部填充应用栏，而不是在顶部显示导航栏")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>总结

本文档提供了有关如何将分层应用程序设计原则应用于简单应用程序的详细说明，以方便在三个移动平台之间重复使用代码： iOS、Android 和 Windows Phone。

它介绍了用于设计应用程序层的过程，并讨论了在每个层中实施 &amp; 功能的代码。

可以从[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)下载代码。

## <a name="related-links"></a>相关链接

- [构建跨平台应用程序（主文档）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可移植示例应用（github）](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
