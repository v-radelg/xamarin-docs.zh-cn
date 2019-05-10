---
title: 绑定 OBJECTIVE-C 库
description: 本文档提供了高级别概述了如何创建C#绑定到 OBJECTIVE-C 代码描述如何将绑定事件、 方法、 自定义控件和的详细信息。
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 3b81ba51a0fbdf4c684ca602cb083f8da08c7d6a
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977989"
---
# <a name="binding-objective-c-libraries"></a>绑定 OBJECTIVE-C 库

当使用 Xamarin.iOS 或 Xamarin.Mac，可能会遇到情况下你想要使用第三方 OBJECTIVE-C 的库。 在这些情况下，您可以使用 Xamarin 绑定项目来创建C#将绑定到本机 OBJECTIVE-C 的库。 该项目使用相同的工具，我们使用 iOS 和 Mac Api，用于将C#。

本文档介绍如何将绑定 Objective C Api，如果要绑定只是 C Api，则应为此，使用标准.NET 机制[P/Invoke framework](https://www.mono-project.com/docs/advanced/pinvoke/)。
如何以静态方式链接 C 库的详细信息位于[链接本机库](~/ios/platform/native-interop.md)页。

请参阅我们的配套[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外，如果你想要了解有关实质上会发生什么情况的详细信息，请检查我们[绑定概述](~/cross-platform/macios/binding/overview.md)页。

绑定可以生成适用于 iOS 和 Mac 库。
此页介绍了如何在绑定，但是 Mac 绑定非常类似 iOS 上运行。

**适用于 iOS 的示例代码**

可以使用[iOS 绑定示例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)项目尝试使用绑定。

<a name="Getting_Started" />

## <a name="getting-started"></a>入门

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

创建绑定的最简单方法是创建 Xamarin.iOS 绑定项目。
您可以执行此操作从 Visual Studio for Mac 通过选择项目类型， **iOS > 库 > 绑定库**:

[![](objective-c-libraries-images/00-sml.png "执行此操作从 Visual Studio for Mac 通过选择项目类型，iOS 库绑定库")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

创建绑定的最简单方法是创建 Xamarin.iOS 绑定项目。
您可以选择执行此 Windows 上从 Visual Studio 项目类型，**可视化C#> iOS > 绑定库 (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "iOS 绑定库 iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意:绑定项目**Xamarin.Mac**仅支持在 Visual Studio for mac。

-----

生成的项目包含可以编辑一个小模板，将包含两个文件：`ApiDefinition.cs`和`StructsAndEnums.cs`。

`ApiDefinition.cs`是将在其中定义的 API 协定，这是介绍如何将基础的 Objective C API 投影到的文件C#。 语法和此文件的内容是本文档讨论的主要主题，它的内容仅限于C#接口和C#委托声明。 `StructsAndEnums.cs`文件是将在此输入所需的任何定义的接口和委托的文件。 这包括枚举值和你的代码可能使用的结构。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>绑定 API

若要执行全面的绑定，将想要了解 Objective C API 定义和熟悉的.NET Framework 设计准则。

若要将绑定的库通常您将开始使用 API 定义文件。 API 定义文件只是C#源文件，其中包含C#接口已通过几种帮助获取绑定的属性批注。  此文件是定义之间协定C#和 OBJECTIVE-C 是。

例如，这是一个库的简单 api 文件：

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

上面的示例定义一个名为类`Cocos2D.Camera`派生`NSObject`基类型 (此类型来自`Foundation.NSObject`) 和用于定义的静态属性 (`ZEye`)，采用任何自变量和方法的两个方法采用三个自变量。

中介绍了 API 文件和可以使用的属性的格式的深入讨论[API 定义文件](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)下面一节。

若要生成完整的绑定，您将通常处理四个组件：

-  API 定义文件 (`ApiDefinition.cs`模板中)。
-  可选： 任何枚举类型、 所需的 API 定义文件的结构 (`StructsAndEnums.cs`模板中)。
-  可选： 额外源可能会展开生成的绑定，或提供更C#友好 API (任何C#向项目添加的文件)。
-  要绑定本机库。

此图表显示在文件之间的关系：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "此图表显示在文件之间的关系")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 定义文件将仅包含命名空间和接口定义 （包含接口可以包含任何成员），且不应包含类、 枚举、 委托或结构。 API 定义文件是只是将用于生成该 API 的协定。

需要让枚举或支持的类应在单独的文件，在"CameraMode"上面的示例上托管任何额外的代码是一个枚举值，CS 文件中不存在，应在单独的文件，例如托管`StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs`文件结合`StructsAndEnum`类，用于生成库的核心绑定。 您可以使用作为生成的库的是，但通常情况下，你将想要优化生成的库来添加一些C#为了用户方便的功能。 一些示例包括实现`ToString()`方法，提供C#索引器，将隐式转换添加到和从某些本机类型或提供一些方法的强类型化版本。 这些改进都存储在额外C#文件。 只将添加C#文件复制到你的项目，它们将包含在此生成过程。

下面的示例演示如何将实现中的代码在`Extra.cs`文件。 请注意，你将使用分部类根据这些增强的组合而生成的分部类`ApiDefinition.cs`和`StructsAndEnums.cs`核心绑定：

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

生成库将生成本机绑定。

若要完成此绑定，应将本机库添加到项目中。  您可以执行此操作通过将本机库添加到项目中，通过拖放本机库从查找工具拖到项目中，解决方案资源管理器，或通过右键单击该项目，然后选择**外** > **将文件添加**选择本机库。
按照约定的本机库以单词"lib"开头和以".a"扩展名结尾。 Visual Studio for Mac 时执行此操作时，将添加两个文件：.a 文件和自动填充C#文件，其中包含有关本机库包含的信息：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "按照约定的本机库与 word 库开头和结尾扩展名.a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

内容`libMagicChord.linkwith.cs`文件包含有关如何使用此库的信息，并指示你的 IDE 以打包到生成的 DLL 文件的此二进制文件：

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

完整的有关如何使用的详细信息 [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
属性中记录了[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)。

现在，生成项目时您会得到与`MagicChords.dll`文件，其中包含的绑定和本机库。 你可以分发此项目或生成的 DLL 到其自己的其他开发人员使用。

有时你可能会发现需要几个枚举值、 委托定义或其他类型。 不要将放那些在 API 定义文件中，因为它只是一个协定

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定义文件

API 定义文件包含多个接口。 API 定义中的接口将打开到类声明，并且它们必须使用修饰[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)属性指定的类的基类。

您可能想知道为什么我们未使用的类而不是接口约定定义。 我们选择了接口，因为它使我们能够编写方法的协定，而无需提供 API 定义文件中，在方法主体，也无需为其提供主体必须引发异常或返回有意义的值。

但由于我们使用接口作为一个框架来生成一个类，我们不得不求助于修饰的属性的约定来驱动该绑定的各个部分。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>绑定方法

可以执行的最简单绑定是将方法绑定。 只需声明中的接口的方法C#命名约定和修饰的方法 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
属性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性是链接在C#Objective C 中具有名称 Xamarin.iOS 运行时名称。 参数 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
属性是 OBJECTIVE-C 的选择器的名称。 示例如下：

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

上面的示例展示了如何将绑定实例方法。 若要绑定的静态方法，必须使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)属性，如下：

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

这是必需的因为协定是一个接口的一部分，接口有没有静态 vs 实例声明的概念，因此再一次需要求助于属性。 如果你想要隐藏绑定中的特定方法，您可以修饰方法替换[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。

`btouch-native`命令将引入引用参数不为 null 检查。 如果你想要允许特定参数的 null 值，使用 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
该参数，此类的属性：

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

将引用类型，导出使用时[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)关键字还可以指定分配语义。 这是需要确保没有数据泄漏。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>绑定属性

就像方法一样使用绑定 OBJECTIVE-C 的属性 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
属性，并直接映射到C#属性。 就像方法，可以使用修饰属性 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
和 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
特性。

当你使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)上盖 btouch 本机下的一个属性的属性实际上会绑定两个方法： getter 和 setter。 提供要导出的名称**basename** setter 计算通过预先计算的单词"set"，打开的第一个字母**basename**为大写和进行需要的选择器自变量。 这意味着，`[Export ("label")]`应用于属性实际上会绑定"标签"和"setLabel:"Objective C 的方法。

有时 Objective C 属性不遵循上面所述的模式和名称，将手动覆盖。 在这些情况下可以控制该绑定通过使用生成的方法 [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
属性 getter 或 setter，例如：

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

这会将绑定"isMenuVisible"和"setMenuVisible:"。 （可选） 可以使用以下语法绑定属性：

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

其中的 getter 和 setter 中显式定义为`name`和`setName`更高版本的绑定。

除了静态属性使用的支持[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)，您可以修饰使用线程静态属性[ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)，例如：

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

就像方法允许部分参数，使用标记[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)，可以将应用 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
到一个属性以指示该 null 是有效的值的属性，例如：

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)还可以直接在 setter 上指定参数：

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>但请注意，自定义控件绑定

设置自定义控件的绑定时，应考虑以下注意事项：

1. **绑定属性必须是静态**-当定义的属性，绑定[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)必须使用属性。
 2. **属性名称必须完全匹配**-用于将属性绑定的名称必须完全匹配的自定义控件中的属性名称。
3. **属性类型必须完全匹配**-用于将属性绑定的变量类型必须完全匹配的自定义控件中的属性的类型。
4. **断点和 getter/setter** -断点放置在 getter 或 setter 方法的属性将永远不会被命中。
5. **观察回调**-你将需要使用观察回调的自定义控件的属性值中的更改通知。

不遵守任何上述列出需要注意的问题可能会导致在运行时以静默方式失败的绑定。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Objective C 可变模式和属性

Objective C 框架使用一些类是使用可变子类不可变的用法。 例如`NSString`是不可变版本，而`NSMutableString`是允许变化的子类。

这些类中很常见的不可变的基本类包含具有 getter 但没有 setter 的属性。 对于要引入 setter 的可变版本。 由于这不是真的可能实现与C#，我们需要将此用法映射到将使用的术语C#。

这将映射到的方法C#是在基类上添加 getter 和 setter，但标记使用的资源库 [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
属性。

然后，在可变的子类，使用 [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
要确保该属性实际重写父行为的属性的属性。

示例:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>绑定的构造函数

`btouch-native`工具将自动生成每构造函数在类中，为给定类`Foo`，它将生成：

-  `Foo ()`： 默认构造函数 （映射到 Objective C 的"init"构造函数）
-  `Foo (NSCoder)`： 在的 NIB 文件反序列化过程中使用的构造函数 (映射到目标-C"initWithCoder:"构造函数)。
-  `Foo (IntPtr handle)`： 构造函数用于基于句柄的创建，这是运行时调用时运行时需要公开从非托管对象的托管的对象。
-  `Foo (NSEmptyFlag)`： 这使用由派生类以防止重复初始化。

对于你定义的构造函数，它们需要使用以下签名的接口定义中声明： 必须返回`IntPtr`值和方法的名称应为构造函数。 例如，绑定`initWithFrame:`构造函数中，这是你将：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>绑定协议

在 API 设计文档中，部分中所述[讨论模型和协议](~/ios/internals/api-design/index.md#Models)，Xamarin.iOS 会 Objective C 协议映射到已使用标记的类 [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
属性。 这通常用来实现 Objective C 委托类。

常规的绑定的类和委托类之间最大的区别是，委托类可能具有一个或多个可选方法。

例如，请考虑`UIKit`类`UIAccelerometerDelegate`，这是如何在 Xamarin.iOS 中绑定：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

由于这是在定义上的可选方法`UIAccelerometerDelegate`无其他执行组件。 但如果在协议上没有所需的方法，您应添加 [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
属性的方法。 这将强制实现的用户实际提供的方法的正文。

一般情况下，响应消息的类中使用协议。 这通常是对象的 Objective C 中通过将响应的协议中方法的实例分配给"委托"属性。

在 Xamarin.iOS 中的约定是任何支持这两个的 Objective C 松散耦合样式的实例`NSObject`可以分配给委托，并还公开它的强类型化版本。 出于此原因，我们通常提供两者`Delegate`强类型化的属性和一个`WeakDelegate`的松散类型化。 我们通常将绑定将松散类型化的版本与[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)，并且我们使用了[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)属性来提供强类型化版本。

这将显示我们的绑定方式`UIAccelerometer`类：

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**MonoTouch 7.0 中的新增功能**

已合并 MonoTouch 从 7.0 开始新的和改进的协议绑定功能。  此项新支持使得更容易使用采用给定类中的一个或多个协议的 Objective C 的惯用语言。

对于每个协议定义`MyProtocol`Objective C 中现`IMyProtocol`列出了该协议，从所有所需的方法的接口，以及提供了所有可选的方法的扩展类。  更高版本，可结合编辑器允许开发人员无需使用单独的上一抽象模型类的子类实现协议方法在 Xamarin Studio 中新的支持。

包含任何定义[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)属性实际上会生成极大提高使用协议的方法的三个支持类：

```csharp
    // Full method implementation, contains all methods
    class MyProtocol : IMyProtocol {
        public void Say (string msg);
        public void Listen (string msg);
    }

    // Interface that contains only the required methods
    interface IMyProtocol: INativeObject, IDisposable {
        [Export ("say:")]
        void Say (string msg);
    }

    // Extension methods
    static class IMyProtocol_Extensions {
        public static void Optional (this IMyProtocol this, string msg);
        }
    }
```

**类实现**提供完整的抽象类可以重写的各个方法并获取完整的类型安全。  但由于C#不支持多重继承，有的方案，可能需要具有不同的基类，但仍想要实现一个接口，即位置

生成**接口定义**传入。  它是具有来自协议的所有所需的方法的接口。  这允许开发人员想要实现您的协议只是实现该接口。  在运行时将自动注册为采用协议类型。

请注意，仅列出了所需的方法和接口确实公开了可选的方法。  这意味着采用协议的类将获得完整的类型检查所需的方法，但将不得不求助于 （手动使用弱类型化 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
属性和匹配签名） 的可选的协议方法。

为了更方便地使用使用协议的 API，绑定工具还将生成一个扩展方法类公开的所有可选方法。  这意味着，只要您正在使用 API，则您将能够将视作具有所有方法的协议。

如果你想要在你的 API 中使用的协议定义，您需要在你的 API 定义中编写主干空接口。  如果你想要使用 MyProtocol API 中，您需要执行此操作：

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

上述需要因为在绑定时间`IMyProtocol`将不存在，就是为什么您需要提供一个空接口。

#### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成接口

任何时候要实现的协议，此类为生成的接口之一：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

接口方法的实现将自动获取导出具有正确的名称，因此等效于此：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

如果隐式或显式实现接口并不重要。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>绑定类扩展

Objective C 中就可以扩展类，使用新方法，在精神到类似C#的扩展方法。 当存在下列方法之一时，可以使用 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
若要标记为 Objective C 消息的接收方的方法的属性。

例如，在 Xamarin.iOS 中我们绑定定义的扩展方法`NSString`时`UIKit`作为中的方法导入`NSStringDrawingExtensions`，如下所示：

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>绑定 OBJECTIVE-C 的自变量列表

Objective C 支持可变参数参数。 例如：

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

调用此方法从C#想要创建的签名如下：

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

这会将声明为内部，该方法隐藏从用户，上面的 API，但将其公开到库。 然后可以编写类似下面的方法：

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>绑定字段

有时想要访问在库中声明的公共字段。

通常这些字段包含必须在引用的字符串或整数值。 它们通常用于为字符串，其中表示特定的通知和作为字典中的键。

若要将字段绑定，将属性添加到接口定义文件，并修饰此属性与[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)属性。 此属性接受一个参数： 要查找的符号的 C 名称。 例如：

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果你想要包装在不是派生的静态类中的各个字段`NSObject`，可以使用 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
此类的类的属性：

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

上述将生成`LonelyClass`这不是派生自`NSObject`并且将包含绑定到`NSSomeEventNotification` 
 `NSString`作为公开`NSString`。

[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)特性可以应用于以下数据类型：

-  `NSString` （仅只读属性） 的引用
-  `NSArray` （仅只读属性） 的引用
-  32 位整数 (`System.Int32`)
-  64 位整数 (`System.Int64`)
-  32 位浮点数 (`System.Single`)
-  64 位浮点数 (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

除了本机字段名称，还可以指定该字段位于何处，通过将库名称传递的库名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

因此需要使用要以静态方式链接，如果是连接要绑定到任何库`__Internal`名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>绑定枚举

您可以添加`enum`直接在您的绑定文件复制到便于使用这些内部 API 定义-而无需使用不同的源文件 （即需要进行编译的绑定和最后一个项目中）。

示例:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

还有可能创建你自己的枚举来替换`NSString`常量。 在这种情况下将生成器**自动**创建方法以将枚举值和 NSString 常量转换为您。

示例:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

在上面的示例中，可以决定进行修饰`void Perform (NSString mode);`与[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。 这会**隐藏**绑定使用者的基于常量的 API。

但是这会限制创建类型为更好的 API 替代使用的子类[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)属性。 这些生成的方法不是`virtual`，即你不会将无法重写它们的或者不可能，是一个不错的选择。

一种替代方法是将标记原始`NSString`-基于定义作为`[Protected]`。 这将允许子类化工作，在需要时，和 wrap'ed 版本仍将起作用并调用重写方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>绑定`NSValue`， `NSNumber`，和`NSString`为更好的类型

[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)特性，绑定`NSNumber`，`NSValue`并`NSString`（枚举） 到了更多准确C#类型。 该属性可用于创建更好、 更准确，基于本机 API 的.NET API。

（上返回值） 的方法、 参数和属性可修饰[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)。 唯一限制是，您的成员**不得**位于内部 [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
或[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)接口。

例如：

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

将输出：

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

在内部，我们可以执行`bool?`  <->  `NSNumber`并`CGRect`  <->  `NSValue`转换。

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 此外支持数组`NSNumber``NSValue`和`NSString`（枚举）。

例如：

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

将输出：

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` 是`NSString`支持的枚举中，我们将提取右侧`NSString`值和处理的类型转换。

请参阅[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)文档，查看所支持的转换类型。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>绑定通知

通知是发布到的消息`NSNotificationCenter.DefaultCenter`，作为一种机制，用于从一部分到另一个应用程序广播消息。 开发人员通常使用[NSNotificationCenter](xref:Foundation.NSNotificationCenter)的[AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification}))方法。 当应用程序将消息发送到通知中心时，它通常包含中存储的有效负载[NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo)字典。 此字典弱类型化，并获取它的信息是容易出错，因为用户通常需要在哪些键位于字典和可以存储在字典中的值的类型的文档中读取。 存在密钥有时可作为一个布尔值。

Xamarin.iOS 绑定生成器提供了对开发人员可以将绑定通知的支持。 若要执行此操作，设置 [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
属性也是属性上的使用标记 [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
属性 （它可以是公共或私有）。

此属性可以使用不带参数不执行任何负载的通知，也可以指定`System.Type`引用在 API 定义中，另一个接口通常与名称以"EventArgs"结尾。 生成器会将接口转换类子类`EventArgs`，将包括所有列出的属性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性应使用 EventArgs 类中，若要列出用于查找要提取的值的 Objective C 字典的键的名称。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上面的代码将生成一个嵌套的类`MyClass.Notifications`使用以下方法：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

你的代码用户然后可以轻松地订阅通知发布到[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)通过使用如下代码：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

中的返回的值`ObserveDidStart`可用于轻松地停止接收通知，如下：

```csharp
token.Dispose ();
```

也可以调用[NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))并传递该令牌。 如果在通知中包含参数，则应指定一个帮助程序`EventArgs`接口，如下：

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

将生成上述`MyScreenChangedEventArgs`类的`ScreenX`并`ScreenY`将提取的数据的属性[NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo)字典使用键"ScreenXKey"和"ScreenYKey"分别并应用正确的转换。 `[ProbePresence]`属性为生成器用来探测如果设置了密钥`UserInfo`，而不是尝试提取的值。 这用于项的状态与的值 （通常为布尔值） 的情况。

这可以编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>绑定类别

类别是用于扩展的类中可用方法和属性集的 OBJECTIVE-C 的机制。   在实践中，它们用于扩展的基类的功能 (例如`NSObject`) 特定框架中的链接时 (例如`UIKit`)，使其方法可用，但仅当新的框架中的链接。   在某些其他情况下，它们用于组织在类中的功能的功能。   它们是在精神到类似C#扩展方法。这是什么类别将如下所示在 Objective c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上面的示例中如果在上找到一个库，将会扩展的实例`UIView`使用方法`makeBackgroundRed`。

若要将这些绑定，可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)接口定义的属性。  使用时 [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
属性的含义 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
属性更改被用来指定基类来扩展，为要扩展的类型。

下面演示如何`UIView`绑定扩展并将其转换为C#扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述操作将创建`MyUIViewExtension`包含的类`MakeBackgroundRed`扩展方法。  在任何这意味着您现在可以调用"MakeBackgroundRed"`UIView`子类中，为您提供相同的功能上 Objective-c。 在某些其他情况下，不要扩展系统类，而是用于组织功能，仅仅是用于修饰，将使用类别。  如：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

尽管可以使用 [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
属性还声明此修饰样式，您可能还只是它们将全部添加到类定义。  这两种将实现该目的：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

它是在这些情况下，合并类别只是较短：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>绑定块

块是由 Apple 将在功能上等同的引入了新的构造C#匿名方法添加到 Objective-c。 例如，`NSSet`类现在公开此方法：

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上面的说明声明一个名为方法`enumerateObjectsUsingBlock:`采用一个参数名为`block`。 这种块是类似于C#，它提供了支持用于捕获当前环境"（this"指针，对本地变量和参数的访问） 中的匿名方法。 中的上述方法`NSSet`调用具有两个参数的块`NSObject`(`id obj`一部分) 和一个布尔值的指针 ( `BOOL *stop`) 部分。

若要将绑定与 btouch 这种类型的 API，需要首先声明块类型签名为C#委托，然后从 API 入口点，此类引用它：

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

现在你的代码可以调用从函数和C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果您愿意，还可以使用 lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>异步方法

绑定生成器可以将某一类的方法转换为适合异步的方法 (返回 Task 的方法&lt;T&gt;)。

可以使用 [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
方法的返回 void，并且其最后一个参数是回调的特性。  绑定生成器时将此应用于方法时，将生成该方法带有后缀的版本`Async`。  如果回调不带任何参数，返回值将是`Task`，如果回调采用参数，则结果将是`Task<T>`。  如果回调采用多个参数，则应设置`ResultType`或`ResultTypeName`来指定所需的生成将保存的所有属性的类型名称。

示例:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上面的代码将生成这两个 LoadFile 方法，以及：

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>公开强类型作为弱 NSDictionary 参数

在 Objective C API 中的许多地方，传递的参数为弱类型`NSDictionary`与特定键和值，但这些 Api 是 （你可以传递无效的密钥，并获取无警告; 可以传递无效的值，并获取无警告） 容易出错和令人沮丧若要使用，因为它们需要文档，以查找可能的密钥名称和值的多个行程。

解决方案是提供提供强类型化版本的 api 和后台映射的各种基础键和值的强类型化的版本。

因此举例来说，如果 Objective C API 接受`NSDictionary`，并记录为采用键`XyzVolumeKey`此方法采用`NSNumber`卷值介于 0.0 到 1.0 和`XyzCaptionKey`采用字符串形式，您会希望用户能够很好的 API如下所示：

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume`属性被定义为可以为 null 的浮点型，因为 Objective C 中的约定不需要具有的值，因此其中可能未设置值的情况下这些字典。

若要执行此操作，需要做一些事情：

* 创建一个强类型化类，该子类[DictionaryContainer](xref:Foundation.DictionaryContainer)并为每个属性提供各种 getter 和 setter。
* 声明为采用的方法的重载`NSDictionary`以采用新的强类型化版本。

可以手动，或者创建强类型化类，也可以使用生成器来为您完成工作。  首先我们将探讨如何手动执行此操作以便您了解正在运行的内容，然后自动的方法。

你需要为此创建支持文件，不会进入您的 API 协定。  这是你将需要编写创建 XyzOptions 类：

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

然后，您应提供包装器方法，它会显示在高级别 API 中的，基于低级别 API。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果你的 API 不需要覆盖，可以通过使用安全地隐藏 NSDictionary 基于 API [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
属性。

正如您所见，我们使用 [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
属性，用于显示新的 API 入口点，以及我们对其使用我们强类型显现`XyzOptions`类。  此包装方法还允许为 null，来传递。

现在，我们没有提到的一点是 where`XyzOptionsKeys`原来的位置值。  通常，您将分，它会显示类似的静态类中的 API 密钥`XyzOptionsKeys`，如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

让我们看一下自动支持创建这些强类型化的字典。  这样可以避免很多样板，可以直接在 API 协定，而不是使用外部文件中定义的字典。

若要创建强类型化的字典，介绍 API 中的接口，并给它装饰[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性。  这将告知生成器，它应使用与你将派生自的接口相同的名称创建一个类`DictionaryContainer`，并将为其提供强类型化访问器。

[ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性采用一个参数，即静态类包含字典键的名称。  然后该接口的每个属性将成为一个强类型化访问器。  默认情况下，代码将使用的属性名称与在静态类中的后缀"密钥"以创建取值函数。

这意味着外部文件，也无需手动创建 getter 和 setter 的每个属性，也无需手动查找密钥，无法再创建将强类型化访问器需要自己。

这是整个绑定将如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

以防您需要在中引用你`XyzOption`成员不同的字段 (即不是名称带有后缀的属性`Key`)，可以修饰的属性 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
你想要使用的名称的属性。

<a name="Type_mappings" />

## <a name="type-mappings"></a>类型映射

本部分介绍了如何将 OBJECTIVE-C 的类型映射到C#类型。

<a name="Simple_Types" />

### <a name="simple-types"></a>简单类型

下表显示了如何应映射到 Xamarin.iOS 世界从 OBJECTIVE-C 和产品 CocoaTouch 世界类型：

|Objective C 类型名称|Xamarin.iOS Unified API 类型|
|---|---|
|`BOOL`， `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([更多有关绑定 NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (另请参阅： [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`， `GLfloat`|`nfloat`|
|CoreFoundation 类型 (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|基础类型 (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>数组

Xamarin.iOS 运行时会自动将负责将转换的C#到数组`NSArrays`，并执行转换，因此，例如虚构的 OBJECTIVE-C 方法返回`NSArray`的`UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

此类绑定：

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

其思路是使用强类型C#数组，因为这将使 IDE 提供正确的代码完成与实际类型而不会迫使用户已经猜到，或查阅文档以找出数组中包含的对象的实际类型。

在其中您可以跟踪向数组中包含的实际派生程度最高类型下的情况下，可以使用`NSObject []`作为返回值。

<a name="Selectors" />

### <a name="selectors"></a>选择器

选择器显示为特殊类型的 Objective C api `SEL`。 在绑定选择器时，将映射到类型`ObjCRuntime.Selector`。  通常选择器中使用对象、 目标对象和一个选择器 API 调用的目标对象中公开。 基本上提供这两种对应于C#委托： 封装要调用的方法以及要调用的方法中的对象的内容。

这是该绑定如下所示：

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

而这是将通常使用方法，例如应用程序中：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

若要使绑定到更好C#开发人员，您通常将提供采用的方法`NSAction`参数，它允许C#而不是使用委托和 lambda `Target+Selector`。 若要这样做通常会隐藏`SetTarget`通过将其与标记的方法 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
属性，然后将公开新的帮助器方法，如下：

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

因此，现在你的用户代码可以编写如下代码：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>字符串

当绑定使用的方法`NSString`，您可以替换，使用C#字符串类型，返回类型和参数。

您可能想要使用的唯一情况`NSString`直接时，此字符串用作为标记。 有关字符串的详细信息和`NSString`，请阅读[NSString 上的 API 设计](~/ios/internals/api-design/nsstring.md)文档。

在某些极少数情况下，API 可能要公开类似于 C 的字符串 (`char *`) 而不是 OBJECTIVE-C 的字符串 (`NSString *`)。 在这些情况下，你可以批注与参数 [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
属性。

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / ref 参数

某些 Api 在其参数中返回值，或由引用传递参数。

通常签名如下所示：

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一个示例显示了一个常用的 Objective C 方法以返回错误代码，一个指向`NSError`传递指针，并返回时设置的值。   第二种方法演示如何 Objective C 方法可能采用对象并修改其内容。   这将是一个引用，而不是纯输出值的处理过程。

你的绑定将如下所示：

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>内存管理属性

当你使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性，并将数据传入被调用方法将保留的您可以通过将其作为第二个参数，例如传递指定的参数的语义：

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

上述标记为具有"保留"语义值。 可用的语义是：

-  Assign
-  复制
-  保留

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>风格指导原则

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>使用 [Internal]

可以使用 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
若要隐藏的公共 API 中的某个方法的属性。 您可能想要在其中公开的 API 是过低级别，您想要提供一个基于此方法的单独文件中的高级实现的情况下执行此操作。

此外可以使用此时遇到绑定生成器中的限制，例如某些高级的方案可能会公开未绑定的类型和想要在您自己的方式，将绑定和你想要自行包装这些类型在您自己的方式。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件处理程序和回调

Objective C 类通常将通知广播或委托类 （Objective C 委派） 上发送消息来请求信息。

此模型中，而完全支持，并按 Xamarin.iOS 有时会比较麻烦。 Xamarin.iOS 公开C#事件模式，并可以在这些情况下使用的类上的方法回调系统。 这样，若要运行如下代码：

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

绑定生成器是能够减少键入映射到的 Objective C 模式所需的C#模式。

它从 Xamarin.iOS 1.4 将有可能还指示生成器以生成特定的 Objective C 委托的绑定并公开作为委托C#事件和主机类型的属性。

有此过程中涉及的两个类、 主机类，它将是当前发出事件，然后将发送到的那个`Delegate`或`WeakDelegate`和实际委托类。

考虑以下设置：

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

要包装类，您必须：

-  在主机类中，将添加到你 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   声明充当其委托的类型和C#公开的名称。 在上述示例中，这些将是`typeof (MyClassDelegate)`和`WeakDelegate`分别。
-  在委托类中，有两个以上的参数，每个方法需要指定要用于自动生成的 EventArgs 类的类型。

绑定生成器并不局限于包装只有单个事件目标，就可以，一些 Objective C 类来发出消息到多个委托，因此必须提供数组以支持此安装程序。 大多数组织不需要它，但生成器已准备好支持这种情况。

将生成的代码：

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

`EventArgs`用于指定的名称`EventArgs`要生成类。 应使用签名每一个 (在此示例中，`EventArgs`将包含`With`类型 nint 属性)。

与上面的定义中，生成器将生成生成 MyClass 中的以下事件：

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

因此您现在可以使用如下代码：

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

回调事件调用一样，区别在于，而不是让多个潜在的订阅服务器 (例如，多个方法可以将挂钩到`Clicked`事件或`DownloadFinished`事件) 回调仅具有单个订阅者。

该过程是完全相同，唯一的区别是，而不是公开的名称`EventArgs`类将生成的 EventArgs 实际上用于命名生成C#委托名称。

如果委托类中的方法返回一个值，绑定生成器会将此映射到而不是事件的父类别中的委托方法。 在这些情况下，你需要提供应返回由方法如果用户未挂接到委托的默认值。 执行此操作使用 [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
或[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)属性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 将返回值进行硬编码时 [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
用于指定将返回哪些输入的参数。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>枚举和基类型

此外可以引用枚举或 btouch 接口定义系统不直接支持的基类型。 若要执行此操作，将枚举和核心类型放入单独的文件，并将这添加为一个 btouch 向提供的额外文件的一部分。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>链接的依赖项

如果绑定不是你的应用程序的一部分的 Api，您需要确保针对这些库已链接到可执行文件。

你需要告知如何链接您的库的 Xamarin.iOS，这可以通过更改生成配置来调用`mtouch`具有一些附加的命令生成指定如何与新的库时使用链接的参数"-gcc_flags"选项，后跟带引号的字符串，其中包含所需的程序，此类的所有额外的库：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上面的示例中将链接`libMyLibrary.a`，`libSystemLibrary.dylib`和`CFNetwork`到最终可执行文件的框架库。

或可以充分利用程序集级别[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，，您可以将它们嵌入在协定文件 (如`AssemblyInfo.cs`)。
当你使用[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，需要能够在作为所做的绑定过程中，这将使用你的应用程序中嵌入本机库时本机库。 例如：

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

您可能想知道，为什么需要`-force_load`命令，原因是-ObjC 标志尽管中的代码对其进行编译，不会保留支持的类别 （链接器/编译器死码消除去除它） 所需的元数据在适用于 Xamarin.iOS，需要在运行时。

<a name="Assisted_References" />

## <a name="assisted-references"></a>协助的引用

某些暂时性对象，如操作工作表和警报框难以跟踪的面向开发人员，并绑定生成器可以一点点帮助。

例如，如果有一个类，演示了一条消息，然后生成`Done`事件，这种处理的传统方法是：

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

在上述情况中，开发人员需要保持对对象的引用，自己和或者泄漏或主动清除框他自己的引用。  尽管绑定代码，生成器支持跟踪的引用，并清除其特殊的方法调用时，上面的代码就会处于：

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

请注意，它不再需要将该变量保留在实例中，它适用于本地变量和不需要对象不活动时，清除该引用。

若要这样做的优点，您的类应具有的事件属性设置[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)声明以及`KeepUntilRef`变量设置为类似对象完成其工作时调用的方法的名称此：

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>继承的协议

自 Xamarin.iOS 3.2 版起，我们支持从已被标记为的协议继承[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)属性。 这在某些 API 模式，如在很有用`MapKit`其中`MKOverlay`协议，继承自`MKAnnotation`协议，并采用了多个类继承的`NSObject`。

从历史上看，我们要求将协议复制到每个实现，但在这些情况下现在我们可拥有`MKShape`类继承自`MKOverlay`协议和它将自动生成所需的所有方法。

## <a name="related-links"></a>相关链接

- [绑定示例](https://developer.xamarin.com/samples/BindingSample/)
