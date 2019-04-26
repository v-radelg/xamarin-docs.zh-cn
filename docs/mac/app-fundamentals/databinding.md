---
title: 数据绑定和键值编码在 Xamarin.Mac 中
description: 本文介绍如何使用键-值的编码和观察以便数据绑定到 Xcode 的 Interface Builder 中的 UI 元素的键-值。
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 4a391160f2102fd1f069a45eb7c16aec91dfd7e0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61427934"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>数据绑定和键值编码在 Xamarin.Mac 中

_本文介绍如何使用键-值的编码和观察以便数据绑定到 Xcode 的 Interface Builder 中的 UI 元素的键-值。_

## <a name="overview"></a>概述

当在 Xamarin.Mac 应用程序中使用 C# 和.NET，有权访问的相同键值对的编码和数据绑定技术的使用的开发人员*Objective C*和*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_到使用而不是编写代码的 UI 元素将数据绑定。

通过使用键-值的编码和数据绑定技术在 Xamarin.Mac 应用程序中，可以极大地减少需要编写和维护来填充和使用 UI 元素的代码量。 此外可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致更易于维护，更灵活的应用程序设计。

[![正在运行的应用程序示例](databinding-images/intro01.png "的正在运行的应用示例")](databinding-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用键-值编码和 Xamarin.Mac 应用程序中的数据绑定的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性使用绑定于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>什么是键值对的编码

键值对的编码 (KVC) 是一种机制间接访问对象的属性、 使用密钥 （特殊格式字符串） 来标识而不是通过实例变量访问它们的属性或访问器方法 (`get/set`)。 在 Xamarin.Mac 应用程序中实现键 / 值编码符合访问器，可以获得其他 macOS （以前称为 OS X） 功能，例如键-值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 的访问权限。

通过使用键-值的编码和数据绑定技术在 Xamarin.Mac 应用程序中，可以极大地减少需要编写和维护来填充和使用 UI 元素的代码量。 此外可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致更易于维护，更灵活的应用程序设计。 

例如，让我们看看 KVC 符合对象的以下类定义：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

首先，`[Register("PersonModel")]`属性注册类，并将它暴露给 Objective-c。 然后，需要从继承类`NSObject`(或子类继承自`NSObject`)，这将添加多个基类允许为 KVC 兼容的方法。 下一步，`[Export("Name")]`属性公开`Name`属性和定义更高版本将用于通过 KVC 和 KVO 方法访问的属性的密钥值。 

最后，若要能够观察到的键-值更改为该属性的值，访问器必须换行中其值将变为`WillChangeValue`并`DidChangeValue`方法调用 (指定与相同的密钥`Export`属性)。  例如：

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

此步骤是_非常_重要在 Xcode 中的数据绑定的 Interface Builder （正如我们将看到在这篇文章中更高版本）。

有关详细信息，请参阅 Apple[键-值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>密钥和密钥路径

一个_密钥_是一个字符串，标识对象的特定属性。 通常情况下，键对应于键值编码符合对象中的访问器方法的名称。 密钥必须使用 ASCII 编码，通常以小写字母开头并且不得包含空格。 给定上述示例中，因此`Name`将密钥值为`Name`属性的`PersonModel`类。 密钥和它们公开的属性名称无需是相同的但在大多数情况下这些。

一个_注册表项路径_是分隔密钥用来指定对象属性层次结构，以遍历点构成的字符串。 序列中的第一个键的属性是相对于接收方，并且每个后续密钥计算相对于以前的属性的值。 相同的方式使用点表示法来遍历对象并将其属性在 C# 类中。

例如，如果您展开`PersonModel`类并添加`Child`属性：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

为子范围的名称的密钥路径将是`self.Child.Name`或只需`Child.Name`（基于如何使用密钥值）。

### <a name="getting-values-using-key-value-coding"></a>获取使用键-值编码的值

`ValueForKey`方法将返回指定键的值 (作为`NSString`)、 相对于接收请求 KVC 类的实例。 例如，如果`Person`的一个实例`PersonModel`上面定义的类：

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

这将返回的值`Name`实例的属性`PersonModel`。 

### <a name="setting-values-using-key-value-coding"></a>使用键-值编码的设置值

同样，`SetValueForKey`将指定的键的值 (作为`NSString`)、 相对于接收请求 KVC 类的实例。 同样，使用的实例`PersonModel`类，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

值会更改`Name`属性设置为`Jane Doe`。

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>观察值更改

使用键-值观察 (KVO)，可以将观察者附加到 KVC 符合类的特定密钥和通知 （使用 KVC 技术或直接访问 C# 代码中的给定的属性），该注册表项的值修改任何时间。 例如：

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

现在，任何时候`Name`的属性`Person`的实例`PersonModel`修改类时，新值写出到控制台。 

有关详细信息，请参阅 Apple[键值观察编程指南简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>数据绑定

以下部分将说明如何使用键-值编码和键-值观察符合类将数据绑定到 Xcode 的 Interface Builder，而不是读取和写入值使用 C# 代码中的 UI 元素。 在这种方式中分离你_数据模型_从用来显示它们的视图，使 Xamarin.Mac 应用程序更加灵活和易于维护。 此外可以显著减少必须编写的代码量。

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>定义数据模型

你可以将数据绑定中 Interface Builder 的 UI 元素之前，必须具有使其作为在 Xamarin.Mac 应用程序中定义的 KVC/KVO 符合类_数据模型_绑定。 数据模型提供的所有数据，将用户界面中显示接收到的数据的用户运行应用程序时在 UI 中进行任何修改。

例如，如果已编写的应用程序管理的员工组，您可以使用以下类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

中介绍了大部分此类的功能[什么是键值对的编码](#What_is_Key-Value_Coding)上面一节。 但是，让我们看看几个特定元素和所进行的允许此类，以用作数据模型的一些新增功能**阵列控制器**并**树控制器**（其中我们稍后将使用的数据将绑定**树视图**，**大纲视图**并**集合视图**)。

第一次，因为某位员工可能是一个管理器，我们已使用`NSArray`(特别是`NSMutableArray`以便可以修改的值) 以允许它们管理要附加到它们的员工：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

此处需要注意两件事：

1. 我们使用了`NSMutableArray`而不是标准的 C# 数组或集合，因为它要求将数据绑定到 AppKit 控件如**表视图**，**大纲视图**和**集合**.
2. 我们通过它强制转换为公开的员工数组`NSArray`数据绑定目的而更改其C#格式的名称， `People`，为数据绑定要求，`personModelArray`形式 **{class_name} 数组**（请注意，第一个字符进行大小写）。

接下来，我们需要添加一些特殊名称公共方法，以支持**阵列控制器**并**树控制器**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

这些行为允许控制器请求和修改他们显示的数据。 如已公开`NSArray`这些更高版本，具有非常特定的命名约定 （这不同于典型 C# 命名约定）：

- `addObject:` -将对象添加到数组。
- `insertObject:in{class_name}ArrayAtIndex:` -其中`{class_name}`是您的类的名称。 此方法将对象插入到给定索引处的数组。
- `removeObjectFrom{class_name}ArrayAtIndex:` -其中`{class_name}`是您的类的名称。 此方法在给定索引处的数组中删除的对象。
- `set{class_name}Array:` -其中`{class_name}`是您的类的名称。 此方法允许您使用一个新替换现有的执行。

在这些方法，我们已包装到数组中的更改`WillChangeValue`和`DidChangeValue`KVO 符合性消息。

最后，由于`Icon`属性的值依赖于`isManager`属性，更改为`isManager`属性可能不会反映在`Icon`的数据绑定 UI 元素 （过程 KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

若要更正的我们使用下面的代码：

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

请注意，除了自己的密钥，`isManager`访问器还会发送`WillChangeValue`和`DidChangeValue`用于消息`Icon`密钥，因此它会看到的更改。

我们将使用`PersonModel`完成这篇文章的剩余部分的数据模型。

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>简单数据绑定

使用我们定义的数据模型，让我们看一下 Xcode 的 Interface Builder 中的数据绑定的简单示例。 例如，让我们将窗体添加到我们的 Xamarin.Mac 应用程序可以用于编辑`PersonModel`上面定义。 我们将添加几个文本字段和一个复选框来显示和编辑我们的模型的属性。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard** Interface Builder 中的文件和其类命名为`SimpleViewController`: 

[![添加新的视图控制器](databinding-images/simple01.png "添加的新视图控制器")](databinding-images/simple01-large.png#lightbox)

接下来，将返回到 Visual Studio for Mac，请编辑**SimpleViewController.cs**文件 （即已自动添加到我们的项目），并公开的一个实例`PersonModel`，我们将数据绑定到窗体。 添加以下代码：

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

接下来加载视图时，让我们创建的实例我们`PersonModel`并填充此代码：

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

现在，我们需要创建窗体，双击**Main.storyboard**文件以打开 Interface Builder 中为进行编辑。 布局窗体以类似如下所示：

[![编辑在 Xcode 中的情节提要](databinding-images/simple02.png "编辑在 Xcode 中的情节提要")](databinding-images/simple02-large.png#lightbox)

进行数据绑定到窗体`PersonModel`，我们通过公开`Person`密钥，执行以下操作：

1. 选择**员工姓名**文本字段并切换到**绑定检查器**。
2. 检查**绑定到**框，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.Name`有关**注册表项路径**: 

    [![输入密钥的路径](databinding-images/simple03.png "输入密钥的路径")](databinding-images/simple03-large.png#lightbox)
3. 选择**职业**文本字段，并检查**绑定到**框，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.Occupation`有关**注册表项路径**:  

    [![输入密钥的路径](databinding-images/simple04.png "输入密钥的路径")](databinding-images/simple04-large.png#lightbox)
4. 选择**员工是经理**复选框，并检查**绑定到**框，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.isManager`有关**注册表项路径**:  

    [![输入密钥的路径](databinding-images/simple05.png "输入密钥的路径")](databinding-images/simple05-large.png#lightbox)
5. 选择**管理的员工数**文本字段，并检查**绑定到**框，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.NumberOfEmployees`有关**注册表项路径**:  

    [![输入密钥的路径](databinding-images/simple06.png "输入密钥的路径")](databinding-images/simple06-large.png#lightbox)
6. 如果员工不是管理员，我们想要隐藏的员工的托管标签号和文本字段。
7. 选择**管理的员工数**标签，展开**Hidden**折叠图标，并检查**绑定到**框，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.isManager`有关**注册表项路径**:  

    [![输入密钥的路径](databinding-images/simple07.png "输入密钥的路径")](databinding-images/simple07-large.png#lightbox)
8. 选择`NSNegateBoolean`从**值转换器**下拉列表中：  

    ![选择 NSNegateBoolean 密钥转换](databinding-images/simple08.png "选择 NSNegateBoolean 密钥转换")
9. 这将告知数据绑定标签将被隐藏，是否的值`isManager`属性是`false`。
10. 重复步骤 7 和 8**管理的员工数**文本字段。
11. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果您运行应用程序中的值`Person`属性将自动填充窗体：

[![显示一个自动填充窗体](databinding-images/simple09.png "显示一个自动填充窗体")](databinding-images/simple09-large.png#lightbox)

用户对窗体的任何更改将返回写入到`Person`视图控制器中的属性。 例如，取消选中**员工是经理**更新`Person`的实例我们`PersonModel`并**管理的员工数**（通过自动隐藏标签和文本字段数据绑定）：

[![隐藏的非经理人员的员工人数](databinding-images/simple10.png "隐藏非管理人员的员工数")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>表视图的数据绑定

现在，我们已有数据开绑定的基础知识，让我们看一下更复杂的数据绑定任务通过使用_数组控制器_和数据绑定到表视图。 有关使用表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard** Interface Builder 中的文件和其类命名为`TableViewController`:

[![添加新的视图控制器](databinding-images/table01.png "添加的新视图控制器")](databinding-images/table01-large.png#lightbox)

接下来，让我们编辑**TableViewController.cs**文件 （即已自动添加到我们的项目） 并公开一个数组 (`NSArray`) 的`PersonModel`，我们将数据绑定到窗体的类。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

就像我们在做`PersonModel`上面类中[定义数据模型](#Defining_your_Data_Model)部分中，我们已公开四个特别命名的公共方法，以便从我们的集合的数组控制器和读取和写入数据`PersonModels`。

接下来加载视图时，我们需要填充我们的数组，此代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

现在，我们需要创建表视图，双击**Main.storyboard**文件以打开 Interface Builder 中为进行编辑。 布局看起来类似于下面的表：

[![设置新的表视图的版式](databinding-images/table02.png "布局的新表视图")](databinding-images/table02-large.png#lightbox)

我们需要添加**数组控制器**提供绑定到我们的表的数据，请执行以下操作：

1. 拖动**数组控制器**从**库检查器**拖到**界面编辑器**:  

    ![从库中选择数组控制器](databinding-images/table03.png "从库中选择数组控制器")
2. 选择**数组控制器**中**界面层次结构**并切换到**属性检查器**:  

    [![选择属性检查器](databinding-images/table04.png "选择属性检查器")](databinding-images/table04-large.png#lightbox)
3. 输入`PersonModel`有关**类名**，单击**加上**按钮，然后添加三个键。 命名它们`Name`，`Occupation`和`isManager`:  

    ![添加所需的密钥路径](databinding-images/table05.png "添加所需的密钥路径")
4. 这将告知数组控制器什么管理一个数组，并且哪些属性应公开 （通过密钥）。
5. 切换到**绑定检查器**并在**内容数组**选择**绑定到**并**表格视图控制器**。 输入**模型的注册表项路径**的`self.personModelArray`:  

    ![输入密钥路径](databinding-images/table06.png "输入的密钥路径")
6. 这会绑定到的数组的数组控制器`PersonModels`我们在我们的视图控制器上公开。

现在，我们需要将表视图绑定到数组控制器，请执行以下操作：

1. 选择表视图和**绑定检查器**:  

    [![选择绑定检查器](databinding-images/table07.png "选择绑定检查器")](databinding-images/table07-large.png#lightbox)
2. 下**表的内容**折叠图标，选择**绑定到**并**数组控制器**。 输入`arrangedObjects`有关**控制器密钥**字段：  

    ![定义控制器键](databinding-images/table08.png "定义控制器键")
3. 选择**表视图单元格**下**员工**列。 在中**绑定检查器**下**值**下转图标，选择**绑定到**并**表视图单元格**。 输入`objectValue.Name`有关**模型的注册表项路径**:  

    [![设置模型项路径](databinding-images/table09.png "设置模型项路径")](databinding-images/table09-large.png#lightbox)
4. `objectValue` 是当前`PersonModel`数组由数组控制器中。
5. 选择**表视图单元格**下**职业**列。 在中**绑定检查器**下**值**下转图标，选择**绑定到**并**表视图单元格**。 输入`objectValue.Occupation`有关**模型的注册表项路径**:  

    [![设置模型项路径](databinding-images/table10.png "设置模型项路径")](databinding-images/table10-large.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行该应用程序，将使用我们的数组填充表`PersonModels`:

[![运行应用程序](databinding-images/table11.png "运行应用程序")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>大纲视图的数据绑定

针对大纲视图的数据绑定是非常类似于针对表视图的绑定。 主要区别是，我们将使用**树控制器**而不是**数组控制器**与大纲视图中提供的绑定的数据。 有关使用大纲视图的详细信息，请参阅我们[大纲视图](~/mac/user-interface/outline-view.md)文档。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard** Interface Builder 中的文件和其类命名为`OutlineViewController`: 

[![添加新的视图控制器](databinding-images/outline01.png "添加的新视图控制器")](databinding-images/outline01-large.png#lightbox)

接下来，让我们编辑**OutlineViewController.cs**文件 （即已自动添加到我们的项目） 并公开一个数组 (`NSArray`) 的`PersonModel`，我们将数据绑定到窗体的类。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

就像我们在做`PersonModel`上面类中[定义数据模型](#Defining_your_Data_Model)部分中，我们已公开四个特别命名的公共方法，以便我们的集合中的树控制器和读取和写入数据`PersonModels`。

接下来加载视图时，我们需要填充我们的数组，此代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

现在，我们需要创建我们大纲视图中，双击**Main.storyboard**文件以打开 Interface Builder 中为进行编辑。 布局看起来类似于下面的表：

[![创建大纲视图](databinding-images/outline02.png "创建大纲视图")](databinding-images/outline02-large.png#lightbox)

我们需要添加**树控制器**提供绑定到我们大纲数据，请执行以下操作：

1. 拖动**树控制器**从**库检查器**拖到**界面编辑器**:  

    ![从库中选择一个树控制器](databinding-images/outline03.png "从库中选择一个树控制器")
2. 选择**树控制器**中**界面层次结构**并切换到**属性检查器**:  

    [![选择属性检查器](databinding-images/outline04.png "选择属性检查器")](databinding-images/outline04-large.png#lightbox)
3. 输入`PersonModel`有关**类名**，单击**加上**按钮，然后添加三个键。 命名它们`Name`，`Occupation`和`isManager`:  

    ![添加所需的密钥路径](databinding-images/outline05.png "添加所需的密钥路径")
4. 这将告知树控制器什么管理一个数组，并且哪些属性应公开 （通过密钥）。
5. 下**树控制器**部分中，输入`personModelArray`有关**子级**，输入`NumberOfEmployees`下**计数**，然后输入`isEmployee`下**叶**:  

    ![设置树控制器密钥路径](databinding-images/outline05.png "设置树控制器关键路径")
6. 这将告知树控制器在哪里可以找到任何子节点、 多少的子节点和如果当前节点具有子节点。
7. 切换到**绑定检查器**并在**内容数组**选择**绑定到**并**文件的所有者**。 输入**模型的注册表项路径**的`self.personModelArray`:  

    ![编辑注册表项路径](databinding-images/outline06.png "编辑注册表项路径")
8. 这会绑定到的数组树控制器`PersonModels`我们在我们的视图控制器上公开。

现在，我们需要将我们的大纲视图绑定到树控制器，请执行以下操作：

1. 选择大纲视图并在**绑定检查器**选择：  

    [![选择绑定检查器](databinding-images/outline07.png "选择绑定检查器")](databinding-images/outline07-large.png#lightbox)
2. 下**大纲视图内容**折叠图标，选择**绑定到**并**树控制器**。 输入`arrangedObjects`有关**控制器密钥**字段：  

    ![设置控制器注册表项](databinding-images/outline08.png "设置控制器注册表项")
3. 选择**表视图单元格**下**员工**列。 在中**绑定检查器**下**值**下转图标，选择**绑定到**并**表视图单元格**。 输入`objectValue.Name`有关**模型的注册表项路径**:  

    [![输入模型项路径](databinding-images/outline09.png "输入模型项路径")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` 是当前`PersonModel`数组由树控制器中。
5. 选择**表视图单元格**下**职业**列。 在中**绑定检查器**下**值**下转图标，选择**绑定到**并**表视图单元格**。 输入`objectValue.Occupation`有关**模型的注册表项路径**:  

    [![输入模型项路径](databinding-images/outline10.png "输入模型项路径")](databinding-images/outline10-large.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行该应用程序，将使用的数组填充大纲`PersonModels`:

[![运行应用程序](databinding-images/outline11.png "运行应用程序")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>集合视图数据绑定

使用集合视图的数据绑定是非常类似于绑定到表视图中，因为数组控制器用于提供数据的集合。 由于集合视图不包含预设的显示格式，则需要提供用户交互反馈并跟踪用户选择更多工作。

> [!IMPORTANT]
> 由于在 Xcode 7 和 macOS 10.11 （和更高版本） 中出现问题，不能在情节提要 (.storyboard) 文件内使用集合视图。 因此，需要继续使用.xib 文件来定义集合视图的 Xamarin.Mac 应用。 请参阅我们[集合视图](~/mac/user-interface/collection-view.md)文档了解详细信息。

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>调试本机故障

在数据绑定中进行了错误的操作可能会导致_本机崩溃_在非托管代码，并会导致完全失败，并在 Xamarin.Mac 应用程序`SIGABRT`错误：

[![本机故障对话框示例](databinding-images/debug01.png "本机故障对话框示例")](databinding-images/debug01-large.png#lightbox)

通常有四个主要原因的本机损坏数据绑定期间：

1. 你的数据模型不继承`NSObject`或其子`NSObject`。
2. 未公开到 Objective C 使用属性`[Export("key-name")]`属性。
3. 对访问器的值中的更改换行`WillChangeValue`并`DidChangeValue`方法调用 (指定与相同的密钥`Export`属性)。
4. 中有错误或错误输入密钥**绑定检查器**Interface Builder 中。

### <a name="decoding-a-crash"></a>解码崩溃

让我们在我们的数据绑定导致本机故障，因此我们可以介绍如何找出并修复它。 在接口生成器中，让我们更改的在集合视图的示例中从第一个标签绑定`Name`到`Title`:

[![编辑的绑定键](databinding-images/debug02.png "编辑的绑定键")](databinding-images/debug02-large.png#lightbox)

让我们保存的更改，切换回 Visual Studio for Mac 与 Xcode 同步和运行我们的应用程序。 当显示集合视图时，应用程序将短暂出现崩溃，`SIGABRT`错误 (如中所示**应用程序输出**Visual Studio for Mac 中) 由于`PersonModel`不会公开一个属性具有键`Title`:

[![绑定错误的示例](databinding-images/debug03.png "绑定错误的示例")](databinding-images/debug03-large.png#lightbox)

如果我们滚动到中的错误的最顶端**应用程序输出**我们可以看到解决此问题的关键：

[![在错误日志中查找问题](databinding-images/debug04.png "错误日志中查找问题")](databinding-images/debug04-large.png#lightbox)

此行将告诉我们，密钥`Title`我们要绑定到的对象上不存在。 如果我们更改绑定回`Name`Interface Builder，保存、 同步，在重新生成和运行，应用程序将按预期方式顺利运行。

## <a name="summary"></a>总结

本文已使用数据绑定和键值编码在 Xamarin.Mac 应用程序的详细的信息。 首先，它介绍了通过使用键-值编码 (KVC) 和密钥值观察 (KVO) 公开到 Objective C 的 C# 类。 接下来，它介绍了如何使用 KVO 符合类和数据将其绑定到 Xcode 的 Interface Builder 中的 UI 元素。 最后，介绍使用复杂数据绑定**阵列控制器**并**树控制器**。


## <a name="related-links"></a>相关链接

- [MacDatabinding 情节提要 （示例）](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding Xib （示例）](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [标准控件](~/mac/user-interface/standard-controls.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [集合视图](~/mac/user-interface/collection-view.md)
- [键-值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [键-值观察编程指南简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Cocoa 绑定编程主题简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Cocoa 绑定引用简介](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
