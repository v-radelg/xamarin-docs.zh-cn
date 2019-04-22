---
title: Xamarin.iOS API 设计
description: 本文档介绍了一些指导原则，用于构建 Xamarin.iOS Api 以及如何与这些相关的 Objective-c。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 9425b26b5cc8fcd9b8a80df422d932c96d52889b
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870360"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API 设计

除了核心是 Mono 的一部分的基类库[Xamarin.iOS](http://www.xamarin.com/iOS)附带了适用于各种 iOS Api 允许开发人员使用 Mono 创建本机 iOS 应用程序的绑定。

Xamarin.iOS 的核心，有是一种互操作的引擎，用于桥接与 OBJECTIVE-C 的世界中，以及适用于 iOS 的基于 C 的 Api，如 CoreGraphics 绑定在 C# 世界并[OpenGL ES](#OpenGLES)。

低级别的运行时的 Objective C 代码进行通信处于[MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime)。 为此，绑定之上[Foundation](#MonoTouch.Foundation)，CoreFoundation，并[UIKit](#MonoTouch.UIKit)提供。

## <a name="design-principles"></a>设计原则

以下是一些 （它们也适用于 Xamarin.Mac，适用于 OBJECTIVE-C 的 Mono 绑定在 macOS 上） 的 Xamarin.iOS 绑定我们设计原则：

- 请按照[Framework 设计准则](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- 开发人员可子类 Objective C 类：

  - 从现有类派生
  - 调用链接到基构造函数
  - 重写方法应通过 C# 重写系统
  - 子类化都应适用于 C# 标准构造

- 不会公开到 OBJECTIVE-C 选择器的开发人员
- 提供一种机制来调用任意 OBJECTIVE-C 库
- 请简单和硬 Objective C 任务可能的 Objective C 的常见任务
- 将 OBJECTIVE-C 的属性公开为 C# 属性
- 公开强类型 API:

  - 提高类型安全性
  - 最大程度减少运行时错误
  - 返回类型上获得 IDE IntelliSense
  - 允许 IDE 弹出文档

- 建议 Api 的 IDE 中探索：

  - 例如，而不是公开一个弱类型化数组，如下：
    
    ```objc
    NSArray *getViews
    ```
    公开强类型，如下：
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    这使 Visual Studio for Mac 能够浏览 API 时，不要自动完成功能，使所有`System.Array`可对返回的值的操作，并允许在 LINQ 中参与的返回值。

- 本机 C# 类型：

  - [`NSString` 将成为 `string`](~/ios/internals/api-design/nsstring.md)
  - 打开`int`并`uint`参数应保持到 C# 枚举和 C# 枚举与枚举`[Flags]`属性
  - 而不是以非特定于类型的`NSArray`对象，将公开为强类型化数组的数组。
  - 有关事件和通知，让用户之间进行选择：

    - 默认情况下强类型版本
    - 用于高级的用例的弱类型版本

- 支持的 Objective C 委托模式：

    - C# 事件系统
    - 公开 C# 委托 (lambda、 匿名方法和`System.Delegate`) 到块的 Objective C Api

### <a name="assemblies"></a>程序集

Xamarin.iOS 包括大量的程序集构成*Xamarin.iOS 配置文件*。 [程序集](~/cross-platform/internals/available-assemblies.md)页提供了更多信息。

### <a name="major-namespaces"></a>主要命名空间 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](xref:ObjCRuntime)命名空间使开发人员可以桥接是世界上之间 C# 和 Objective-c。
这是专门针对 iOS、 基于从 Cocoa # 和 Gtk # 体验而设计的新绑定。

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

[Foundation](xref:Foundation)命名空间提供的基本数据类型旨在与属于 iOS Objective C Foundation 框架进行互操作和它是面向对象编程在 OBJECTIVE-C 中的基础

Xamarin.iOS 反映在 C# 中从 OBJECTIVE-C 类的层次结构 例如，OBJECTIVE-C 的基本类[NSObject](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html)可以从 C# 中，通过[Foundation.NSObject](xref:Foundation.NSObject)。

尽管此命名空间提供了基础的 Objective C 的基础类型的绑定，但在少数情况下我们具有映射的基础类型到.NET 类型。 例如：

- 而不是应对[NSString](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)并[NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，运行时将它们作为 C# 公开[字符串](xref:System.String)s 和强类型化[数组](xref:System.Array)整个 sAPI。

- 此处公开各种帮助器 Api，以允许开发人员可以将绑定第三方 Objective C Api，其他 iOS Api 或当前未绑定的 Xamarin.iOS 的 Api。

绑定 Api 的详细信息，请参阅[Xamarin.iOS 绑定生成器](~/cross-platform/macios/binding/binding-types-reference.md)部分。


##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject)类型是 Objective C 的所有绑定的基础。 Xamarin.iOS 类型镜像 iOS 产品 CocoaTouch Api 中的类型的两个类: （通常称为 CoreFoundation 类型） 的 C 类型和 Objective C 类型 （这些都派生 NSObject 类）。

对于每个镜像的非托管的类型的类型，则可以获取本机对象通过[处理](xref:Foundation.NSObject.Handle)属性。

虽然 Mono 将提供的所有对象，垃圾回收`Foundation.NSObject`实现[System.IDisposable](xref:System.IDisposable)接口。 这意味着可以显式释放任何给定 NSObject 的资源而无需等待垃圾回收器来启动中。 使用大量 NSObjects，例如，可能会保存到较大的数据块的指针的 UIImages 时，这很重要。

如果您的类型需要执行确定性终结，重写[NSObject.Dispose(bool) 方法](xref:Foundation.NSObject.Dispose(System.Boolean))Dispose 的参数"bool disposing"，如果设置为 true，它表示，因为调用 Dispose 方法和用户在对象上的显式调用的 Dispose （)。 如果值为 false，这意味着，在 Dispose (bool disposing) 方法正在从终结器上调用终结器线程。 []()


##### <a name="categories"></a>类别

它从 Xamarin.iOS 8.10，可以从 C# 创建 OBJECTIVE-C 的类别。

这是使用`Category`属性，指定要将扩展为该属性的参数的类型。 例如，下面的示例将扩展 NSString。

    [Category (typeof (NSString))]

每个类别方法使用普通的机制用于将方法导出为 Objective C 使用`Export`属性：

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

所有托管的扩展方法必须是静态的但可以创建用于 C# 中的扩展方法使用标准语法的 Objective C 实例方法：

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

并且扩展方法的第一个参数将在其调用方法的实例。

完整的示例：

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

此示例将添加到 NSString 类，该类可从 OBJECTIVE-C 调用本机 toUpper 实例方法

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

这非常有用的一种方案将方法添加到您的代码库中的类的整个集，例如，这将使所有`UIViewController`实例报告，它们可以旋转：

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```


##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute 阶段处理时应用程序以减少其大小是用于告知 mtouch – Xamarin.iOS 部署工具 — 以保留一个类型或类型的成员的自定义属性。

每个未被应用程序静态链接的成员都可能被删除。 因此，此属性用于将成员的未静态引用，但仍需在应用程序的标记。

例如，如果你动态实例化类型，则需要保留类型的默认构造函数。 如果你使用 XML 序列化，则需要保留类型的属性。

你可以在类型的每个成员上或类型本身应用此属性。 如果你想要保留整个类型，可以使用语法 [保留 (AllMembers = true)] 的类型。

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit)命名空间包含到的所有用户界面组件组成的 C# 类形式的 CocoaTouch 的一对一映射。 已修改该 API 遵循 C# 语言中使用的约定。

C# 委托提供用于常见操作。 请参阅[委托](#Delegates)部分，了解详细信息。

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

OpenGLES，对于我们分发[修改后版本](xref:OpenTK)的[OpenTK](http://www.opentk.com/) API，为已修改为使用 CoreGraphics 数据类型和结构，OpenGL 的面向对象的绑定，以及仅将公开可在 iOS 的功能。

OpenGLES 1.1 功能可通过 ES11.GL 类型，所述[此处](xref:OpenTK.Graphics.ES11.GL)类型。

OpenGLES 2.0 功能可通过 ES20.GL 类型，所述[此处](xref:OpenTK.Graphics.ES20.GL)类型。

OpenGLES 3.0 功能可通过 ES30.GL 类型，所述[此处](xref:OpenTK.Graphics.ES30.GL)类型。


### <a name="binding-design"></a>绑定设计

Xamarin.iOS 不只是绑定到基础 OBJECTIVE-C 的平台。 它所扩展的.NET 类型系统和调度系统到更好地混合 C# 和 Objective-c。

就像 P/Invoke 是一个有用的工具来调用 Windows 和 Linux 上的本机库或作为 IJW 支持可用于在 Windows 上的 COM 互操作，Xamarin.iOS 扩展了运行时以支持 C# 对象绑定到 OBJECTIVE-C 的对象。

在接下来的几个部分对于不是必需的用户创建 Xamarin.iOS 应用程序，但将帮助开发人员讨论了解如何在操作完成和创建更复杂的应用程序时帮助他们。



#### <a name="types"></a>类型

进行有意义的而不是低级别的基础类型，C# 领域公开 C# 类型。  这意味着[API 使用 C#"string"类型，而不是 NSString](~/ios/internals/api-design/nsstring.md)并使用强类型化的 C# 数组而不是公开 NSArray。

一般情况下，在 Xamarin.iOS 和 Xamarin.Mac 设计中，基础`NSArray`对象未公开。 相反，在运行时将自动转换`NSArray`到强类型化数组的一些`NSObject`类。 因此，Xamarin.iOS 不公开类似 GetViews 返回 NSArray 的弱类型化方法：

```csharp
NSArray GetViews ();
```

相反，该绑定公开强类型化的返回值，如下：

```csharp
UIView [] GetViews ();
```

有几个方法中公开`NSArray`，极端情况，可能想要使用的`NSArray`直接，但其用途，建议不要使用 API 绑定中。

此外，在**经典 API**而不是公开`CGRect`，`CGPoint`并`CGSize`从 CoreGraphics API 中，我们替换为拥有`System.Drawing`实现`RectangleF`， `PointF`和`SizeF`因为它们将帮助开发人员保留使用 OpenTK 的现有 OpenGL 代码。 当使用新的 64 位**Unified API**，应使用 CoreGraphics API。

<a name="Inheritance" />

#### <a name="inheritance"></a>继承

Xamarin.iOS API 设计允许开发人员一样，它们将会扩展 C# 类型，在派生类中，使用"override"关键字，以及链接使用"base"的 C# 关键字的基实现来扩展本机 OBJECTIVE-C 的类型。

此设计允许开发人员能够避免与 OBJECTIVE-C 选择器的处理，其开发过程，因为整个 Objective C 系统已包装在 Xamarin.iOS 库。


#### <a name="types-and-interface-builder"></a>类型和 Interface Builder

当您创建的类型由 Interface Builder 创建实例的.NET 类时，需要提供的构造函数采用单个`IntPtr`参数。
这是绑定与非托管对象的托管的对象实例所需的。
该代码包含单个行，如下：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>委托

Objective C 和 C# 中的每种语言具有不同的含义，对于 word 委托。

在 OBJECTIVE-C 的世界中，并将找到有关产品 CocoaTouch 联机文档中，委托通常是类的一组方法将响应的实例。 在于方法并不总是必需，这是非常类似于 C# 接口，二者的区别。

这些委托发挥重要作用 UIKit 和其他产品 CocoaTouch Api 中。 它们用于完成各项任务：

-  若要向你的代码 （类似于 C# 或 Gtk + 中的事件传递） 提供通知。
-  若要实现数据可视化效果控件模型。
-  若要驱动控件的行为。


编程模式旨在最大程度减少创建派生类可以更改控件的行为。 此解决方案是在精神上类似于其他 GUI 工具包做了什么多年来：Gtk 的发出信号，Qt 槽、 Winforms 事件、 WPF/Silverlight 事件，等等。 若要避免出现数百个接口 （一个用于每个操作），或要求开发人员实现他们不需要太多方法，Objective C 支持可选的方法定义。 这是不同于 C# 接口需要实现的所有方法。

在 OBJECTIVE-C 的类中，您将看到使用此编程模式的类公开的属性，通常称为`delegate`，这是需要实现该接口的必需部分和零个或多个可选部分。

在 Xamarin.iOS 中提供三个互相排斥机制来绑定到这些委托：

1.  [通过事件](#Via_Events)。
2.  [通过强类型化`Delegate`属性](#StrongDelegate)
3.  [通过为松散类型化`WeakDelegate`属性](#WeakDelegate)

例如，考虑[UIWebView](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html)类。 这将调度到[UIWebViewDelegate](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html)实例，该值将赋给[委托](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate)属性。

<a name="Via_Events" />

##### <a name="via-events"></a>通过事件

对于许多类型，Xamarin.iOS 会自动创建相应委托将转发`UIWebViewDelegate`到 C# 事件上的调用。 对于 `UIWebView`：

-  [WebViewDidStartLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:)方法映射到[UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted)事件。
-  [WebViewDidFinishLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:)方法映射到[UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished)事件。
-  [WebView:didFailLoadWithError](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:)方法映射到[UIWebView.LoadError](xref:UIKit.UIWebView.LoadError)事件。

例如，此简单程序记录加载 web 查看开始和结束时间：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>通过属性

可能有多个订阅服务器对事件时事件非常有用。 此外，事件仅限于情况下没有的代码没有返回值。

该代码应该返回值的情况下，我们选择了改为属性。 这意味着只有一个方法，可以设置在给定时间的对象中。

例如，可以使用此机制可消除键盘上的处理程序在屏幕上`UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField`的`ShouldReturn`属性在这种情况下将作为参数返回布尔值，并确定是否文本字段应该做一些工作与返回按钮按下的委托。 在我们的方法，我们会返回 *，则返回 true*给调用方，但我们也从屏幕中删除键盘 (发生这种情况是当文本字段调用`ResignFirstResponder`)。

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>强类型化通过委托属性

如果您不想使用事件，可以提供您自己[UIWebViewDelegate](xref:UIKit.UIWebViewDelegate)子类并将其分配给[UIWebView.Delegate](xref:UIKit.UIWebView.Delegate)属性。 后分配 UIWebView.Delegate，UIWebView 事件调度机制将不再起作用，并且将发生相应事件时被调用 UIWebViewDelegate 方法。

例如，此简单类型记录加载 web 视图所需的时间：

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

上述代码中使用了如下：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述操作将创建 UIWebViewer 和它将指示它将消息发送到通知程序，我们创建对消息作出响应的类的实例。

此模式还用来控制对于某些控件，例如在 UIWebView 情况下，行为[UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad)属性允许`UIWebView`到控件的实例是否`UIWebView`将加载页上或不。

该模式还用于根据几个控件提供的数据。 例如， [UITableView](xref:UIKit.UITableView)控件是一个功能强大的表呈现控件 – 和外观和内容由的实例[UITableViewDataSource](xref:UIKit.UITableViewDataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>松散类型化的通过 WeakDelegate 属性

除了强类型化的属性，还有，开发人员可根据需要以不同的方式绑定内容的弱类型化的委托。
强类型化 everywhere`Delegate`属性将显示在 Xamarin.iOS 的绑定，相应`WeakDelegate`还公开属性。

使用时`WeakDelegate`，你有责任正确修饰类使用[导出](xref:Foundation.ExportAttribute)属性来指定选择器。 例如：

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

请注意，一次`WeakDelegate`属性分配`Delegate`属性不能。 此外，如果您希望 [导出] 继承基类中实现该方法，必须将其公共方法。


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>映射到 C 的 Objective C 委托模式&#35;

当你看到如下所示的 Objective C 示例：

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

这会指示要创建和构造类"SomethingDelegate"的实例并将值分配给 foo 变量的委托属性的语言。 Xamarin.iOS 支持此机制和 C# 语法是：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 中我们提供了映射到 Objective C 的强类型化类委托类。 若要使用它们，您将是子类化和重写由 Xamarin.iOS 的实现定义的方法。 有关它们的工作原理的详细信息，请参阅部分"模型"下面。


##### <a name="mapping-delegates-to-c35"></a>映射到 C 的委托&#35;

UIKit 通常在两个窗体中使用 OBJECTIVE-C 的委托。

第一种形式提供给组件的模型的接口。 例如，作为一种机制来提供有关对视图中，如列表视图的数据存储设施的需求的数据。  在这些情况下，应始终创建适当的类的实例并将分配该变量。

在以下示例中，我们提供`UIPickerView`使用字符串的模型的实现：

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

第二种形式是提供事件的通知。 在这些情况下，尽管我们仍公开了上述，窗体中的 API，但我们还提供 C# 事件，它应该是更易于使用的快速操作和使用匿名委托和 C# 中的 lambda 表达式集成在一起。

例如，您可以订阅`UIAccelerometer`事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

它们很有用，但作为一名程序员必须选择一个或另一个位置，可以使用两个选项。 如果创建你自己的强类型化的响应方/委托的实例，并将其分配，C# 事件不会正常工作。 如果使用 C# 事件，将永远不会调用响应程序/委托类中的方法。

前面的示例使用`UIWebView`可以使用 C# 3.0 lambda 像这样编写：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>对事件作出响应

Objective C 代码，有时多个控件和提供程序的信息的多个控件的事件处理程序将在托管同一个类中。 这是可能的因为类响应消息，并且只要类响应消息，可以将对象链接在一起。

如先前所述，Xamarin.iOS 支持这两个 C# 基于事件的编程模型，和 OBJECTIVE-C 的委托模式，其中您可以创建的新类实现委托，并重写了所需的方法。

它还有可能支持 Objective C 的模式响应程序用于多个不同操作所有托管类的同一实例中。 但为此，你将需要使用 Xamarin.iOS 绑定的低级别功能。

例如，如果您希望您对同时做出响应的类`UITextFieldDelegate.textFieldShouldClear`： 消息和`UIWebViewDelegate.webViewDidStartLoad`： 在类的相同实例中，您必须使用 [导出] 特性声明：

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

C#方法并不重要; 名称最重要的就是传递给 [Export] 特性的字符串。

在使用这种编程风格，确保 C# 参数匹配的运行时引擎会将传递的实际类型。

<a name="Models" />

#### <a name="models"></a>Models

在 UIKit 存储设施或响应程序使用的帮助器类实现中，这些通常称为 Objective C 代码中的代理，以及作为协议实现它们。

Objective C 协议如下所示接口，但它们支持可选的方法-不是所有的方法，即需要为要处理的协议实现。

有两种方法的实现模型。 可以手动实现，也可以使用现有的强类型的定义。


当你尝试实现的类，Xamarin.iOS 尚未绑定时，手动机制是必需的。 它是很容易实现：

-  标记您的类具有运行时的注册
-  你想要重写每个方法应用 [导出] 特性与实际的选择器名称
-  实例化类，并将其传递。

例如，以下可选的方法之一中实现 UIApplicationDelegate 协议定义：

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

OBJECTIVE-C 选择器名称 ("applicationDidFinishLaunching:") 使用 Export 特性声明和使用注册的类`[Register]`属性。

Xamarin.iOS 提供强类型的声明，可供使用，不需要手动绑定。 若要支持此编程模型，Xamarin.iOS 运行时会在类声明支持 [模型] 特性。 这会通知运行时，它应在类中，所有方法不绑定，除非的方法是显式实现。

这意味着，在 UIKit 中，类表示一种协议与可选方法是编写如下代码：

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

当你想要实现模型仅实现的一些方法时，只需是重写你感兴趣，并忽略其他方法的方法。 在运行时仅将挂接覆盖方法，不向 Objective C 的原始方法。

与前面的手动示例等效项是：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

优点是没有必要为 Objective C 标头文件，以查找选择器和类型的参数或映射到 C# 的深入了解，并且可获取 intellisense 从 Visual Studio for Mac 中，以及强类型


#### <a name="xib-outlets-and-c35"></a>XIB 输出口和 C&#35;

> [!IMPORTANT]
> 本部分介绍与输出口的 IDE 集成，使用 XIB 文件时。 当使用适用于 iOS 的 Xamarin 设计器，这所有替换输入下的一个名称**标识 > 名称**IDE，如下所示的属性部分中：
>
> [![](images/designeroutlet.png "在 iOS 设计器中输入的项名称")](images/designeroutlet.png#lightbox)
>
>IOS 设计器的详细信息，请查看[iOS 设计器简介](~/ios/user-interface/designer/introduction.md#how-it-works)文档。

这是低级别说明如何使用 C# 集成输出口，为 Xamarin.iOS 的高级用户提供。 使用 Visual Studio for Mac 映射完成时自动使用在后台为您生成航班上的代码。

在设计时使用 Interface Builder 用户界面，您仅设计应用程序的外观，并将建立一些默认的连接。 如果你想要以编程方式提取信息、 更改在运行时控件的行为或修改在运行时控制，则需要将某些控件绑定到您的托管代码。

这是在几个步骤：

1.  添加**输出口声明**到你**文件的所有者**。
1.  连接到您的控件**文件的所有者**。
1.  存储用户界面以及到 XIB/NIB 文件的连接。
1.  加载 NIB 文件在运行时。
1.  输出口变量的访问。


用于构建使用 Interface Builder 的界面的 Apple 的文档中介绍了通过 (3) 的步骤 (1)。

在使用 Xamarin.iOS，你的应用程序将需要创建派生自 UIViewController 类。 它实现它像这样：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然后从 NIB 文件加载您 ViewController，您执行此操作：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

这将从 NIB 加载用户界面。 现在，若要访问输出口，就需要告知运行时，我们想要对其进行访问。 若要执行此操作，`UIViewController`子类需要声明的属性，并对其具有 [连接] 特性进行批注。 如：

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

属性实现是一个实际提取并存储的实际本机类型的值。

不需要担心这时使用 Visual Studio for Mac 和 InterfaceBuilder。 Visual Studio for Mac 自动镜像，代码编译为你的项目的一部分的分部类中的所有声明的插座。

#### <a name="selectors"></a>选择器

Objective C 编程的核心概念是选择器。 你通常将会遇到要求您传递一个选择器，或需要您的代码以响应一个选择器的 Api。

在 C# 中创建新的选择器是非常简单 – 只需创建的新实例`ObjCRuntime.Selector`类，并使用需要它的 API 中的任何位置中的结果。 例如：

```csharp
var selector_add = new Selector ("add:plus:");
```

对于 C# 方法响应的选择器调用，它必须继承自`NSObject`必须使用选择器名称使用修饰类型和 C# 方法`[Export]`属性。 例如：

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

请注意该选择器名称**必须**完全相同，包括所有中间空格和尾随冒号 (":")，如果存在。

#### <a name="nsobject-constructors"></a>NSObject 构造函数

大多数类中派生的 Xamarin.iOS`NSObject`将公开特定于对象的功能的构造函数，但它们还将公开不会立即的各种构造函数。

按如下所示使用构造函数：

```csharp
public Foo (IntPtr handle)
```

使用此构造函数来实例化类时运行时需要将您的类映射到非托管类。 可以将 XIB/NIB 文件加载时，将发生这种情况。  此时，OBJECTIVE-C 运行时将创建一个对象在非托管领域中，并将调用此构造函数以初始化托管的端。

通常情况下，您需要做是调用基构造函数使用句柄参数，并在正文中，执行所需的任何初始化。

```csharp
public Foo ()
```

这是一个类的默认构造函数，在 Xamarin.iOS 中提供的类，这之间，初始化 Foundation.NSObject 类和所有的类，并在结束时，链接这 Objective C 到`init`类上的方法。

```csharp
public Foo (NSObjectFlag x)
```

此构造函数用于初始化该实例，但阻止代码在结束时调用的 Objective C"init"方法。 通常使用此命令时已注册以进行初始化 (当使用`[Export]`对构造函数) 或当你已掌握你通过另一个平均值的初始化。

```csharp
public Foo (NSCoder coder)
```

此构造函数提供的用例，该对象从 NSCoding 实例初始化的位置。 有关详细信息，请参阅 Apple 的[存档和序列化编程指南。](https://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Exceptions

Xamarin.iOS API 设计不会为 C# 异常引发 Objective C 异常。 设计强制执行，第一个位置中没有垃圾回收发送到 OBJECTIVE-C 的世界和过无效的数据之前，必须生成任何异常的错误绑定本身传递到 OBJECTIVE-C 的世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中，开发人员可以订阅广播的基础平台的通知。 这是通过使用`NSNotificationCenter.DefaultCenter.AddObserver`方法。 `AddObserver`方法采用两个参数之一是你想要订阅的通知; 另一个是引发通知时要调用的方法。

在 Xamarin.iOS 和 Xamarin.Mac，各种通知的密钥都托管于触发通知的类。 例如，通知引起`UIMenuController`作为托管`static NSString`中的属性`UIMenuController`"通知"的名称结尾的类。

### <a name="memory-management"></a>内存管理

Xamarin.iOS 具有将负责为您释放资源，它们不再使用时的垃圾回收器。 除了垃圾回收器的所有对象都派生自`NSObject`实现`System.IDisposable`接口。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公开`IDisposable`接口是协助开发人员在释放对象可能会封装较大的内存块的简便方法 (例如，`UIImage`可能看起来类似于只是正常的指针，但无法将指向的 2 个兆字节映像) 和其他重要、 最有限的资源 （如视频解码缓冲区）。

NSObject 实现 IDisposable 接口，也[.NET Dispose 模式](https://msdn.microsoft.com/library/fs2xkftw.aspx)。 这允许开发人员该子类 NSObject 重写 Dispose 行为并释放其自身的按需资源。 例如，考虑会保留一系列图像此视图控制器：

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

当释放托管的对象时，就不再有用。 你可能仍然需要对对象的引用，但该对象是用于所有目的和用途此时无效。 一些.NET Api 通过引发 ObjectDisposedException，如果你尝试访问已释放的对象上的任何方法，例如确保这一点：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以访问变量"映像"，实际上是无效的引用，不再指向保留图像的 OBJECTIVE-C 的对象。

但是，释放在 C# 中的对象并不意味着一定会销毁该对象。 只需发布 C# 有对对象的引用。 它是可能的 Cocoa 环境可能会保持围绕供自己使用的引用。 例如，如果 UIImageView 的 Image 属性设置为图像，然后释放该映像，则基础 UIImageView 将曾经需要自己的引用并将保留对此对象的引用，直到完成使用它。

#### <a name="when-to-call-dispose"></a>何时调用 Dispose

在需要 Mono 中摆脱您的对象时，应调用 Dispose。 Mono 并不知道你 NSObject 实际上保存到内存或信息池等重要资源的引用时，可能用例。 在这些情况下，应调用 Dispose 以立即释放的内存，而不是等待进行垃圾回收周期的 Mono 的引用。

在内部，当 Mono 创建[NSString 引用从 C# 字符串](~/ios/internals/api-design/nsstring.md)，它将销毁它们立即以降低垃圾回收器必须执行的工作量。 将运行大约要处理的更快地 GC 的更少对象。

#### <a name="when-to-keep-references-to-objects"></a>当要保留对对象的引用

自动内存管理具有一个负面影响是，GC 将消除未使用的对象，只要有任何对其的引用。 这有时可以产生令人惊讶的负面影响，例如，如果创建一个本地变量来保存您的顶层视图控制器，或后面支持你的顶级窗口中，，然后让这些消失。

如果不要在您的对象保留在你的静态引用或实例变量，Mono 将值得庆幸的是，调用 dispose （） 方法，并将会发布对对象的引用。 由于这可能仅未完成的引用，OBJECTIVE-C 运行时将为您销毁对象。

## <a name="related-links"></a>相关链接

- [绑定字段](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
