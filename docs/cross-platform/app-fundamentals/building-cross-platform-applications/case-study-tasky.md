---
title: 跨平台应用案例研究：Tasky
description: 本文档介绍如何 Tasky Portable 示例应用程序的设计和构建为跨平台移动应用程序。 它讨论了应用程序的要求、 接口、 数据模型、 核心功能，实现中，和的详细信息。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 23deddae61452d532a87c51cc1ec3bc53eb91c9f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61278914"
---
# <a name="cross-platform-app-case-study-tasky"></a>跨平台应用案例研究：Tasky

*Tasky* *可移植*是一个简单的待办事项列表应用程序。 本文档介绍了它是如何设计和生成示例，请遵循的指导[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档。 讨论涵盖以下几个方面：

<a name="Design_Process" />

## <a name="design-process"></a>设计过程

建议创建的路线图为你想要实现之前开始编码。 这是跨平台开发的尤其如此生成可能会显示在多个方法的功能。 从开始的要生成的内容节省了时间和精力在开发周期的后期清晰的思路。

 <a name="Requirements" />

### <a name="requirements"></a>要求

设计应用程序的第一步是确定所需的功能。 这些可以是高级目标或详细的用例。 Tasky 具有简单的功能要求：

 -  查看任务列表
 -  添加、 编辑和删除任务
 -  设置任务的状态设置为完成

应考虑特定于平台的功能的使用。  Tasky 可以充分利用 iOS 地理围栏或 Windows Phone 动态磁贴？ 即使在第一个版本，不使用特定于平台的功能，您应计划继续请确保你的业务和数据层可以容纳它们。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>用户界面设计

开始可实现跨目标平台的高级设计。 请注意注意平台特定 UI 约束。 例如，`TabBarController`在 iOS 中可以显示五个按钮，而 Windows Phone 等效项可以显示最多四个。
绘制屏幕流使用所选 （纸张适用） 的工具。

 [![](case-study-tasky-images/taskydesign.png "绘制屏幕流使用所选纸张工作原理的工具")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>数据模型

了解哪些数据需要存储将有助于确定要使用哪个持久性机制。 请参阅[跨平台数据访问](~/cross-platform/app-fundamentals/index.md)有关可用的存储机制和它们之间做出决定的帮助信息。 对于此项目，我们将使用 SQLite.NET。

Tasky 需要为每个 TaskItem 存储三个属性：

 -  **名称**– 字符串
 -  **说明**– 字符串
 -  **完成**– 布尔值

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

请考虑在用户界面将需要使用以满足要求的 API。 待办事项列表需要以下函数：

 -   **列出的所有任务**-显示所有可用任务的主屏幕都列表
 -  **获取一个任务**– 当任务行数
 -  **将一个任务保存**– 时编辑任务
 -  **删除一个任务**– 当任务被删除
 -  **创建空任务**– 时创建一个新任务

若要实现代码重用，此 API 应一次在中实现*可移植类库*。

 <a name="Implementation" />

### <a name="implementation"></a>实现

一旦应用程序设计已协商好，请考虑如何它可能会作为一个跨平台应用程序实现。 这将成为应用程序的体系结构。 中的指南[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档中，应用程序代码应被破坏向下插入以下几个部分：

 -   **公共代码**– 公共项目包含用于存储任务数据的可重用代码; 公开 Model 类和一个 API，用于管理保存和加载的数据。
 -   **特定于平台的代码**– 实现利用作为后端则公共代码对于每个操作系统，本机 UI 的特定于平台的项目。

 [![](case-study-tasky-images/taskypro-architecture.png "特定于平台的项目实现利用作为后端的常见代码为每个操作系统的本机 UI")](case-study-tasky-images/taskypro-architecture.png#lightbox)

以下各节所述这两部分。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>公共 (PCL) 代码

可移植的 tasky 为共享通用代码使用可移植类库的策略。 请参阅[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)代码共享选项的说明的文档。

所有常见的代码，包括数据访问层、 数据库代码和约定，放置在类库项目中。

完整的 PCL 项目如下图所示。 所有可移植库中的代码适用于每个目标平台。 在部署时，每个本机应用程序将引用该库。

![](case-study-tasky-images/portable-project.png "每个本机应用程序部署时，将引用此库")

下面的类关系图显示了按层进行分组的类。 `SQLiteConnection`类是从 Sqlite NET 包的样板代码。 类的其余部分是用于 Tasky 自定义代码。 `TaskItemManager`和`TaskItem`类表示特定于平台的应用程序公开的 API。

 [![](case-study-tasky-images/classdiagram-core.png "TaskItemManager 和 TaskItem 类表示特定于平台的应用程序公开的 API")](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空间到单独的层可帮助管理每个层之间的引用。 特定于平台的项目应只需包括`using`业务层的语句。 应通过公开 api 封装的数据访问层和数据层`TaskItemManager`业务层中。

 <a name="References" />

### <a name="references"></a>参考资料

可移植类库需要可以跨多个平台，每个都有不同的平台和框架功能的支持级别可用。 正因为如此，存在一些的限制的包和框架库可用。 例如，Xamarin.iOS 不支持 c#`dynamic`关键字，因此可移植类库不能使用取决于动态代码的任何包，即使此类代码会在 Android 上运行也是如此。 Visual Studio for Mac 将阻止你添加不兼容的包和引用，但您需要请记住，在以后避免出现意外的限制。

注意:你将看到你的项目引用未使用的框架库。 这些引用是 Xamarin 项目模板的一部分。 时编译的应用程序，链接过程将会删除未引用的代码，因此，即使`System.Xml`已被引用，它将不会包含在最终应用程序因为我们不使用任何 Xml 的函数。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>数据层 (DL)

数据层包含执行数据 – 的物理存储到数据库、 平面文件或其他机制的代码。 Tasky 数据层由两部分组成： SQLite NET 库和添加进行了自定义代码。

Tasky 依赖于 Sqlite net nuget 包 （由 Frank Kreuger 发布） 将提供的对象关系映射 (ORM) 数据库接口的 SQLite NET 代码嵌入。 `TaskItemDatabase`类继承自`SQLiteConnection`并添加所需的创建、 读取、 更新、 删除 (CRUD) 方法来读取和将数据写入到 SQLite。 很可能是在其他项目中重复使用的泛型 CRUD 方法的一个简单的样板实现。

`TaskItemDatabase`是一个单例，确保针对同一个实例发生的所有访问。 使用锁来防止从多个线程并发访问。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>在 Windows Phone 上的 SQLite

IOS 和 Android 都提供 SQLite 作为操作系统的一部分，而 Windows Phone 不包含兼容的数据库引擎。 若要在所有三个平台间共享代码的 SQLite 的 Windows phone 本机版本是必需的。 请参阅[使用本地数据库](~/xamarin-forms/app-fundamentals/databases.md)有关设置 Sqlite Windows Phone 项目的详细信息。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用接口来通用化数据访问

数据层依赖于`BL.Contracts.IBusinessIdentity`，以便它可以实现需要具有主键的抽象的数据访问方法。 实现接口的任何业务层类随后会保存在数据层中。

此接口只需指定一个整数属性，使其作为主关键字：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

类的基类实现接口，并添加 SQLite NET 属性将其标记为自动递增的主键。 业务层实现此基类中的任何类随后会保存在数据层：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

使用接口的泛型方法的数据层中的一个示例是这`GetItem<T>`方法：

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

一个[锁](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx)内实现`TaskItemDatabase`类，以防止对数据库的并发访问。 这是为了确保来自不同线程的并发访问序列化 （否则 UI 组件可能会尝试在后台线程正在更新一次读取数据库）。 锁的实现方式的示例如下所示：

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

可以在其他项目中重新使用的大部分数据层代码。 层中的仅特定于应用程序代码是`CreateTable<TaskItem>`调用中`TaskItemDatabase`构造函数。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>数据访问层 (DAL)

`TaskItemRepository`类封装与允许的强类型 API 的数据存储机制`TaskItem`对象来创建、 删除、 检索和更新。

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用条件编译

类使用条件编译来设置文件位置-这是实现平台分歧的示例。 返回的路径的属性将编译为每个平台上的不同代码。 代码和特定于平台的编译器指令如下所示：

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

根据平台，则输出将为"<app
path>/Library/TaskDB.db3"适用于 iOS，"<app
path>/Documents/TaskDB.db3"适用于 Android 或只是"TaskDB.db3"为 Windows Phone。

### <a name="business-layer-bl"></a>业务层 (BL)

业务层实现模型类和对其进行管理的外观。
该模型是在 Tasky`TaskItem`类和`TaskItemManager`实现以提供一个用于管理 API 的外观模式`TaskItems`。

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` 包装`DAL.TaskItemRepository`提供 Get，保存和删除应用程序和 UI 层将引用的方法。

业务规则和逻辑被置此处必要 – 例如任何保存对象之前必须满足的验证规则。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>对于特定于平台的代码的 API

公共代码已写入后，必须生成用户界面收集和显示由其公开的数据。 `TaskItemManager`类实现的外观模式来提供应用程序代码的一个简单的 API 来访问。

每个特定于平台的项目中编写的代码通常将与该设备的本机 SDK 紧密耦合，并且只能访问使用定义的 API 的常见代码`TaskItemManager`。 这包括方法和业务类，它公开，如`TaskItem`。

映像将不在平台之间共享，而独立添加到每个项目。 这非常重要，因为每个平台处理图像以不同的方式，使用不同的文件名、 目录和解决方法。

其余各节讨论 Tasky UI 的特定于平台的实现细节。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS 应用

有少量的实现 iOS Tasky 使用常见的 PCL 项目来存储和检索数据的应用程序所需的类。 完整的 iOS 的 Xamarin.iOS 项目如下所示：

 ![](case-study-tasky-images/taskyios-solution.png "如下所示的 iOS 项目")

类显示在此图中，分组到层。

 [![](case-study-tasky-images/classdiagram-android.png "类显示在此图中，分组到层")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

IOS 应用程序引用特定于平台的 SDK 库 – 例如。 Xamarin.iOS 和 MonoTouch.Dialog-1。

它还必须引用`TaskyPortableLibrary`PCL 项目。
在引用列表如下所示：

 ![](case-study-tasky-images/taskyios-references.png "在引用列表如下所示")

使用这些引用此项目中实现了应用程序层和用户界面层。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

应用程序层包含绑定到 UI PCL 所公开的对象所需的特定于平台的类。 特定于 iOS 的应用程序具有两个类可帮助将显示任务：

 -   **EditingSource** – 此类用于将任务的列表绑定到用户界面。 因为`MonoTouch.Dialog`使用了对于任务列表中，我们需要实施此帮助器，若要启用中的轻扫删除功能`UITableView`。 轻扫删除是在 iOS 中，但不是 Android 或 Windows Phone 上常见的因此 iOS 特定项目是实现该接口只有一个。
 -   **TaskDialog** – 此类用于将单个任务绑定到 UI。 它使用`MonoTouch.Dialog`反射 API 来包装`TaskItem`具有包含要允许具有正确的格式输入的屏幕的正确特性的类的对象。

`TaskDialog`类使用`MonoTouch.Dialog`属性来创建屏幕基于类的属性。 该类如下所示：

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

请注意`OnTap`属性需要方法名称 – 这些方法必须在类中存在其中`MonoTouch.Dialog.BindingContext`创建 (在这种情况下，`HomeScreen`类下一节所述)。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>用户界面层 (UI)

用户界面层包含以下类：

1.   **AppDelegate** – 包含对外观 API 调用，以设置字体和颜色在应用程序中使用的样式。 Tasky 是一个简单应用程序中运行的任何其他初始化任务，因此`FinishedLaunching`。
2.   **屏幕**– 的子类`UIViewController`用于定义每个屏幕和其行为。 屏幕将关联在一起的 UI 与应用程序层类和公共 API ( `TaskItemManager` )。 在代码中，在此示例中创建屏幕，但无法对设计使用 Xcode 的 Interface Builder 或情节提要设计器。
3.   **映像**– 可视元素是每个应用程序的重要组成部分。 Tasky 具有初始屏幕和图标图像，必须在常规模式和 Retina 分辨率提供适用于 iOS 的。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

在主页屏幕是`MonoTouch.Dialog`显示的 SQLite 数据库中的任务列表的屏幕。 它继承自`DialogViewController`并实现代码来设置`Root`包含一系列`TaskItem`显示对象。

 [![](case-study-tasky-images/ios-taskylist.png "它从 DialogViewController 继承并实现代码来设置要包含用于显示 TaskItem 对象的集合的根")](case-study-tasky-images/ios-taskylist.png#lightbox)

与显示和任务列表与交互相关的两个主要方法包括：

1.   **PopulateTable** – 使用业务层`TaskManager.GetTasks`方法来检索一系列`TaskItem`要显示的对象。
2.   **所选**– 时访问某行时，显示新屏幕中的任务。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

任务详细信息是允许要进行编辑或删除任务的输入的屏幕。

使用 tasky`MonoTouch.Dialog`的反射 API，以显示屏幕上，因此，没有`UIViewController`实现。 相反，`HomeScreen`类实例化并显示`DialogViewController`使用`TaskDialog`类从应用程序层。

此屏幕截图显示了演示应用空屏幕`Entry`属性中设置水印文本**名称**并**说明**字段：

 [![](case-study-tasky-images/ios-taskydetail.png "此屏幕截图显示了演示设置水印文本的名称和说明字段中的项属性的空屏幕")](case-study-tasky-images/ios-taskydetail.png#lightbox)

功能**任务的详细信息**必须在中实现 （如保存或删除任务） 的屏幕`HomeScreen`类，因为这就是`MonoTouch.Dialog.BindingContext`创建。 以下`HomeScreen`方法支持任务的详细信息屏幕：

1.   **ShowTaskDetails** – 创建`MonoTouch.Dialog.BindingContext`呈现屏幕。 它创建输入的屏幕使用反射来检索属性名称和类型从`TaskDialog`类。 具有属性的属性上实现的其他信息，例如，输入框的水印文本。
2.   **SaveTask** – 此方法中引用`TaskDialog`类通过`OnTap`属性。 它时，将调用**保存**按下时，并使用`MonoTouch.Dialog.BindingContext`以保存所做的更改使用之前检索用户输入数据`TaskItemManager`。
3.   **DeleteTask** – 此方法中引用`TaskDialog`类通过`OnTap`属性。 它使用`TaskItemManager`删除使用主键 （ID 属性） 的数据。

 <a name="Android_App" />

## <a name="android-app"></a>Android App

完整的 Xamarin.Android 项目是如下图所示：

 ![](case-study-tasky-images/taskyandroid-solution.png "与以下所示的 android 项目")

类图中，与按层进行分组的类中：

 [![](case-study-tasky-images/classdiagram-android.png "包含按层进行分组的类的类关系图")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

Android 应用程序项目必须引用特定于平台的 Xamarin.Android 程序集访问类从 Android SDK。

它还必须引用 PCL 项目中 （例如。 TaskyPortableLibrary) 来访问公共数据和业务层代码。

 ![](case-study-tasky-images/taskyandroid-references.png "若要访问的常见的数据和业务层代码的 TaskyPortableLibrary")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

类似于我们在 iOS 版本更早版本，应用程序层中的 Android 版本包含绑定到 UI 的核心所公开的对象所需的特定于平台的类。

 **TaskListAdapter** – 若要显示的列表<T>我们需要实现一个适配器显示自定义对象中的对象`ListView`。 适配器控制哪种布局使用列表中每个项目 – 在这种情况下该代码使用 Android 的内置布局`SimpleListItemChecked`。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面 (UI)

Android 应用的用户界面层是代码和 XML 标记的组合。

 -   **资源/布局**– 屏幕布局和行单元格作为 AXML 文件实现的设计。 AXML 可以手工编写或布局的直观地使用适用于 Android 的 Xamarin UI 设计器。
 -   **资源/Drawable** – 图像 （图标） 和自定义按钮。
 -   **屏幕**-定义每个屏幕和其行为的活动子类。 将联系在一起，与应用程序层类 UI 和常见 API (`TaskItemManager`)。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

在主页屏幕包含活动子类`HomeScreen`和`HomeScreen.axml`文件用于定义布局 （按钮和任务列表的位置）。 屏幕如下所示：

 [![](case-study-tasky-images/android-taskylist.png "屏幕外观如下所示")](case-study-tasky-images/android-taskylist.png#lightbox)

主页屏幕代码定义单击的按钮单击列表中的项，以及填充的列表中的处理程序`OnResume`方法 （因此，它反映了任务的详细信息屏幕中所做的更改）。 使用业务层的加载数据`TaskItemManager`和`TaskListAdapter`从应用程序层。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

任务详细信息屏幕还组成`Activity`子类和 AXML 布局文件。 布局确定输入控件的位置和C#类定义了要加载和保存的行为`TaskItem`对象。

 [![](case-study-tasky-images/android-taskydetail.png "该类定义了要加载和保存 TaskItem 对象的行为")](case-study-tasky-images/android-taskydetail.png#lightbox)

对 PCL 库的所有引用都是通过`TaskItemManager`类。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone App
完整的 Windows Phone 项目：

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone 应用完整的 Windows Phone 项目")

下图显示分组到层的类：

 [![](case-study-tasky-images/classdiagram-wp7.png "此图显示分组到层的类")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

特定于平台的项目必须引用所需的特定于平台的库 (如`Microsoft.Phone`和`System.Windows`) 创建有效的 Windows Phone 应用程序。

它还必须引用 PCL 项目中 （例如。 `TaskyPortableLibrary`) 以利用`TaskItem`类和数据库。

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary 利用 TaskItem 类和数据库")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

同样，与 iOS 和 Android 版本，应用程序层包含非可视元素，可帮助将数据绑定到用户界面。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

Viewmodel PCL 中的数据换行 ( `TaskItemManager`)，并可供 Silverlight/XAML 数据绑定的方式呈现。 这是行为的特定于平台的一个示例 （如跨平台应用程序文档中所述）。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面 (UI)

XAML 有一个唯一的数据绑定功能，可以在标记中声明并减少要显示的对象所需的代码量：

1.   **页**– XAML 文件和其代码隐藏定义的用户界面并引用 Viewmodel 和 PCL 项目中，可以显示和收集数据。
2.   **映像**– 初始屏幕、 背景和图标图像是用户界面的关键部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 类使用`TaskListViewModel`为使用 XAML 的数据绑定功能显示数据。 页面的`DataContext`设置为异步填充的视图模型。 `{Binding}` XAML 中的语法确定数据的显示方式。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

通过绑定来显示每个任务`TaskViewModel`到 TaskDetailsPage.xaml 中定义的 XAML。 通过检索的任务数据`TaskItemManager`业务层中。

 <a name="Results" />

## <a name="results"></a>结果

生成的应用程序每个平台上如下所示：

 <a name="iOS" />

#### <a name="ios"></a>iOS

应用程序使用 iOS 标准用户界面设计，如添加按钮要定位的导航栏中，使用内置**加号 （+）** 图标。 它还使用默认值`UINavigationController`后退按钮行为和支持轻扫到删除表中的。

 [![](case-study-tasky-images/ios-taskylist.png "它还将使用默认 UINavigationController 后退按钮行为，并支持轻扫--删除表中")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "它还使用默认 UINavigationController后退按钮行为和支持轻扫--删除表中")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

Android 应用程序使用内置控件包括需要显示 tick 的行的内置布局。 除了屏幕上后退按钮支持硬件/系统后行为。

 [![](case-study-tasky-images/android-taskylist.png "除了屏幕上后退按钮支持硬件/系统后行为")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "硬件/系统后行为支持除了屏幕后退按钮")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

Windows Phone 应用使用的标准布局，填充屏幕而不是顶部导航栏底部应用栏。

 [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 应用程序使用标准的布局，填充而不是在顶部导航栏屏幕底部应用栏")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 应用程序使用标准布局，填充而不是在顶部导航栏屏幕底部应用栏")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>总结

本文档提供了如何应用简单的应用程序，以便重复使用代码在三个移动平台之间的分层应用程序设计原则的详细的说明： iOS、 Android 和 Windows Phone。

它描述用于设计应用程序层的过程，并讨论哪些代码&amp;已在每个层中实现的功能。

可以从下载代码[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)。

## <a name="related-links"></a>相关链接

- [生成跨平台应用程序 （主要文档）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可移植的示例应用 (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
