---
title: 在 Xamarin iOS 和 Android 的 visual Basic.NET
description: 本演练演示如何生成使用 Visual Basic.NET 编写业务逻辑的本机 Xamarin.iOS 和 Xamarin.Android 应用。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f62d3cb076019ba49303f2c82f009975d9fbdc50
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61163224"
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>在 Xamarin iOS 和 Android 的 visual Basic.NET

[TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)示例应用程序演示了如何通过 Xamarin 使用 Visual Basic 代码编译到可移植类库。 下面是生成的应用在 iOS、 Android 和 Windows Phone 上运行的一些屏幕快照：

 [![](native-apps-images/image5.png "iOS、 Android 和 Windows 手机运行使用 Visual Basic 构建的应用")](native-apps-images/image5.png#lightbox)

IOS、 Android 和 Windows Phone 项目中的示例用编写C#。 使用本机技术构建每个应用程序的用户界面 (情节提要、 Xml 和 Xaml 分别)，而`TodoItem`管理提供的 Visual Basic 可移植类库使用`IXmlStorage`提供实现本机项目。

## <a name="sample-walkthrough"></a>示例演练

本指南介绍如何将 Visual Basic 实现中[TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)适用于 iOS 和 Android 的 Xamarin 示例。

> [!NOTE]
> 在查看说明[可移植 Visual Basic.NET](index.md)然后再继续执行本指南。

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

只能在 Visual Studio 中创建 Visual Basic 可移植类库。
示例库包含我们在四个 Visual Basic 文件中的应用程序的基础知识：

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

由于文件访问行为可大大不同的平台，因此不提供可移植类库`System.IO`文件存储 Api 中的任何配置文件。 这意味着，如果我们想要直接与我们的可移植代码中的文件系统进行交互，我们需要每个平台上回调到我们的本机项目。  通过编写一个简单的界面，可实现在针对 Visual Basic 代码C#在每个平台上，我们可以仍有权访问文件系统的可共享 Visual Basic 代码。

示例代码使用此界面非常简单，包含两个方法： 读取和写入序列化的 Xml 文件。

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS、 Android 和 Windows Phone 实现此接口将稍后在本指南中所示。

### <a name="todoitemvb"></a>TodoItem.vb

此类包含要在整个应用程序中使用的业务对象。 它将在 Visual Basic 中定义和共享与 iOS、 Android 和 Windows Phone 项目用C#。

类定义如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

此示例使用 XML 序列化和反序列化器加载和保存的 TodoItem 对象。

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

管理器类提供了用于可移植代码的 API。 它提供了基本的 CRUD 操作执行`TodoItem`类，但没有实现这些操作。

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

构造函数采用 IXmlStorage 的实例作为参数。 这样，每个平台提供其自己的工作实现同时还介绍了可以共享其他功能的可移植代码。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

存储库类包含用于管理的 TodoItem 对象列表的逻辑。 完整代码如下所示 – 逻辑存在主要是为了跨 TodoItems 管理唯一的 ID 值，添加和从集合中移除。

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> 此代码是一种非常基本的数据存储机制的示例。
> 它被提供演示了如何可移植类库可以编写代码直接使用接口来访问特定于平台的功能 （在此情况下，加载和保存的 Xml 文件）。 它它不应是一个生产质量数据库的替代方法。

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS、 Android 和 Windows Phone 应用程序项目

本部分包含 IXmlStorage 接口的特定于平台的实现，并演示如何使用每个应用程序中。 应用程序项目用编写C#。

### <a name="ios-and-android-ixmlstorage"></a>iOS 和 Android IXmlStorage

Xamarin.iOS 和 Xamarin.Android 提供了完整`System.IO`功能，从而使您可以轻松地加载和保存 Xml 文件中使用以下类：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

IOS 应用程序中`TodoItemManager`并`XmlStorageImplementation`中创建**AppDelegate.cs**文件在此代码片段所示。 前四行只要生成数据将存储位置; 文件的路径最后两行代码显示两个类进行实例化。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

Android 应用程序中`TodoItemManager`并`XmlStorageImplementation`中创建**Application.cs**文件在此代码片段所示。 前三行只要生成数据将存储位置; 文件的路径最后两行代码显示两个类进行实例化。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

应用程序代码的其余部分是主要关心的问题的用户界面，以及用`TaskMgr`类来加载和保存`TodoItem`类。

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone 不提供完全访问设备的文件系统，而公开 IsolatedStorage API。 `IXmlStorage` Windows Phone 的实现如下所示：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

`TodoItemManager`并`XmlStorageImplementation`中创建**App.xaml.cs**文件在此代码片段所示。

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Windows Phone 应用程序的其余部分包含的 Xaml 和C#创建用户界面和使用`TodoMgr`类来加载和保存`TodoItem`对象。

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic 在 Visual Studio for Mac 的 PCL

Visual Studio for Mac 不支持 Visual Basic 语言-无法创建或编译 Visual Basic 项目中的使用 Visual Studio for mac。

Visual Studio for Mac 的可移植类库的支持意味着它可以引用 PCL 程序集从 Visual Basic 生成的。

本部分介绍如何编译 Visual Studio 中的 PCL 程序集，然后确保将会存储在版本控制系统中并由其他项目引用。

### <a name="keeping-the-pcl-output-from-visual-studio"></a>从 Visual Studio 使 PCL 输出

默认情况下将配置大多数版本控制系统 （包括 TFS 和 Git） 为忽略 **/bin/** 目录，这意味着已编译的 PCL 程序集将不会存储。 这意味着您需要手动将其复制到运行 Visual Studio for Mac 中添加对它的引用的任何计算机。

若要确保版本控制系统可以存储 PCL 程序集输出，可以创建将其复制到项目根目录的后期生成脚本。 此生成后步骤可帮助确保可以轻松地添加到源代码管理和与其他项目共享程序集。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. 右键单击项目并选择**属性 > 生成事件**部分。

2. 添加_后期生成_脚本，用于将输出 DLL 从此项目复制到项目根目录 (即外部 **/bin/**)。 根据版本控制配置，该 DLL 现在应能够添加到源代码管理。

  [![](native-apps-images/image6-vs-sml.png "生成事件后生成脚本，以便复制 VB DLL")](native-apps-images/image6-vs.png#lightbox)

下一次生成项目，可移植类库程序集将复制到项目根目录，并检查在/提交/推送所做的更改 DLL 可以存储 （以便可以下载到 Mac 使用 Visual Studio for Mac）。

  [![](native-apps-images/image8-sml.png "文件输出视觉基本程序集的位置")](native-apps-images/image8.png#lightbox)


此程序集可以然后被添加到 Xamarin 项目在 Visual Studio for Mac 中，即使 Xamarin iOS 或 Android 项目中不支持 Visual Basic 语言本身也是如此。

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>引用在 PCL 中的在 Visual Studio for Mac

因为 Xamarin 不支持 Visual Basic 它不能加载 PCL 项目中 （或 Windows Phone 应用），如以下屏幕截图中所示：

 [![](native-apps-images/image9.png "Visual Studio Mac 解决方案")](native-apps-images/image9.png#lightbox)

我们仍然可以在 Xamarin.iOS 和 Xamarin.Android 项目中包括 Visual Basic PCL 程序集 DLL:

1.  右键单击**引用**节点，然后选择**编辑引用...**

    [![](native-apps-images/image10.png "项目编辑引用菜单")](native-apps-images/image10.png#lightbox)

1.  选择 **.Net 程序集**选项卡上，并导航到 Visual Basic 项目目录中的输出 DLL。 即使 Visual Studio for Mac 不能打开项目时，所有文件都应该有从源代码管理。 单击**外**然后**确定**若要将此程序集添加到 iOS 和 Android 应用程序。

    [![](native-apps-images/image11-sml.png "单击添加然后选择确定将此程序集添加到 iOS 和 Android 应用程序")](native-apps-images/image11.png#lightbox)

1.  IOS 和 Android 应用程序现在可以包括 Visual Basic 可移植类库提供的应用程序逻辑。 此屏幕截图显示了一个引用 Visual Basic PCL 并从相应的库使用的功能的代码的 iOS 应用程序。

    [![](native-apps-images/image12-sml.png "编辑引用添加.NET 程序集窗口")](native-apps-images/image12.png#lightbox)


如果更改到 Visual Basic 项目中，Visual Studio 记住要生成项目，请在源代码管理中存储生成的程序集 DLL，然后提取从源代码管理到你的 Mac 上该新 DLL，以便于 Visual Studio for Mac 构建包含最新功能。


## <a name="summary"></a>总结

本文演示了如何使用 Visual Basic 代码中使用 Visual Studio 和可移植类库的 Xamarin 应用程序。 尽管 Xamarin 不直接支持 Visual Basic，编译到一个 PCL 的 Visual Basic 允许使用要包含在 iOS 和 Android 应用中的 Visual Basic 编写的代码。

## <a name="related-links"></a>相关链接

- [TaskyPortableVB （示例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [使用.NET Framework (Microsoft) 的跨平台开发](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
