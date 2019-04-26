---
title: 绑定类型参考指南
description: 本参考指南说明了各种属性和所需了解创建时的概念C#对 OBJECTIVE-C 的库的绑定。
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: d460bf867ce09e614be76d0a4a7ffef01420cf82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266500"
---
# <a name="binding-types-reference-guide"></a>绑定类型参考指南

本文档介绍了可用于批注来驱动绑定在 API 协定文件并生成代码的特性列表

Xamarin.iOS 和 Xamarin.Mac API 协定用C#主要作为接口定义来定义 Objective C 代码出现的方式C#。 该过程包括接口声明以及 API 约定可能需要一些基本类型定义的组合。 绑定类型的简介，请参阅我们的配套指南[绑定 OBJECTIVE-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>类型定义

语法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

已在协定定义中的每个接口[ `[BaseType]` ](#BaseTypeAttribute)声明生成对象的基类型的属性。 在以上声明`MyType`类C#将生成绑定到 OBJECTIVE-C 的类型名为的类型`MyType`。

如果在类型名称后指定任何类型 (在上面的示例`Protocol1`并`Protocol2`) 使用接口继承语法这些接口的内容会进行内联像返回了的协定的一部分`MyType`。
Xamarin.iOS 图面类型采用一种协议是通过的方式初始化所有的方法和协议中声明为类型本身的属性。

下面演示如何的 Objective C 声明`UITextField`将 Xamarin.iOS 协定中定义：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

为此类，则会写入C#API 协定：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

可以通过将其他属性应用于该接口，以及配置控制的代码生成许多其他方面[ `[BaseType]` ](#BaseTypeAttribute)属性。


### <a name="generating-events"></a>生成事件

Xamarin.iOS 和 Xamarin.Mac API 设计的一个功能是，我们将映射 Objective C 委托类作为C#事件和回调。 用户可以选择在每个实例的基础是否要采用的 Objective C 编程模式，通过将分配给属性，如`Delegate`或通过实现将调用 OBJECTIVE-C 运行时的各种方法的类的实例选择C#的样式的事件和属性。

让我们看到如何使用 OBJECTIVE-C 的模型的一个示例：

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

在上述示例中，您可以看到，我们已选择覆盖两种方法，其中一个通知，滚动事件已执行的位置，第二个是布尔值指示应返回的回调`scrollView`是否应该滚动到或未排名靠前。

C#模型，您的库的用户来侦听通知使用C#事件语法或属性语法来挂接预期返回值的回调。

这是如何将C#相同的功能如下所示使用 lambda 的代码：

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

由于事件不会返回的值 （它们具有 void 返回类型） 可以连接多个副本。 `ShouldScrollToTop`不是事件，而是具有类型的属性`UIScrollViewCondition`具有此签名：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它将返回`bool`值，在这种情况下 lambda 语法使我们能够只返回的值从`MakeDecision`函数。

生成事件和链接等类的属性支持的绑定生成器`UIScrollView`使用其`UIScrollViewDelegate`（也称之为 Model 类），这是通过批注您[ `[BaseType]` ](#BaseTypeAttribute)定义替换`Events`和`Delegates`参数 （如下所述）。 除了对进行批注[ `[BaseType]` ](#BaseTypeAttribute)利用这些参数有必要以通知生成器的几个更多的组件。

采用多个参数的事件 （Objective C 中的约定是委托类中的第一个参数是发件人对象的实例） 必须为生成提供你想名称`EventArgs`类。 这通过[ `[EventArgs]` ](#EventArgsAttribute) Model 类的方法声明上的属性。 例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述声明将生成`UIImagePickerImagePickedEventArgs`派生的类`EventArgs`和包这两个参数`UIImage`和`NSDictionary`。 生成器将生成此：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它公开中的以下`UIImagePickerController`类：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

模型方法的返回值会以不同的方式绑定。 那些需要这两个名称生成的C#委托 （该方法的签名） 以及默认值以返回以防用户不提供自己的实现。 例如，`ShouldScrollToTop`定义这是：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述操作将创建`UIScrollViewCondition`签名与委托的，上面所示，如果用户不提供实现，则返回值将为 true。

除了[ `[DefaultValue]` ](#DefaultValueAttribute)属性，也可以使用[ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)属性，指示要调用或中返回指定的参数的值的生成器[ `[NoDefaultValue]` ](#NoDefaultValueAttribute)指示生成器不存在默认值的参数。

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

语法：

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

您使用`Name`属性控制此类型将绑定到 OBJECTIVE-C 的世界中的名称。 这通常用于为C#键入的名称符合.NET Framework 设计准则，但它映射到不会遵循该约定的 Objective C 中的名称。

示例中，我们将在以下情况下映射 Objective C`NSURLConnection`键入到`NSUrlConnection`，如.NET Framework 设计准则使用而不是"URL"的"Url":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名称用作值生成的`[Register]`绑定中的属性。 如果`Name`未指定，则该类型的短名称使用的值作为`[Register]`中生成的输出属性。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events 和 BaseType.Delegates

这些属性用于驱动的新一代C#-设置事件中生成的类的样式。 使用它们将链接其 Objective C delegate 类具有的给定的类。 你将会遇到很多情况下，一个类，其中使用委托类将发送通知和事件。 例如`BarcodeScanner`将具有一个辅助性`BardodeScannerDelegate`类。 `BarcodeScanner`类通常具有`Delegate`属性的一个实例会分配`BarcodeScannerDelegate`来，而此工作原理，您可能想要向用户公开C#-如样式事件接口，并在这些情况下请使用`Events`并`Delegates`的属性[ `[BaseType]` ](#BaseTypeAttribute)属性。

这些属性始终一起设置和必须具有相同数目的元素保持同步。`Delegates`数组包含一个字符串，对于你想要换行，每个弱类型的委托和`Events`数组包含你想要将与之关联的每个类型的一种类型。

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

如果创建此类的新实例时应用此特性，该对象的实例将保留围绕到所引用的方法`KeepRefUntil`已调用。 这可用于提高的易用性 Api，如果不想在用户都可以保留对周围的对象的引用以使用你的代码。 此属性的值是在方法的名称`Delegate`类，因此必须与结合使用这`Events`和`Delegates`属性。

下面的示例显示如何使用此`UIActionSheet`Xamarin.iOS 中：

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

当此特性应用于接口定义它将阻止生成默认构造函数生成器。

需要要使用的类中的其他构造函数之一进行初始化的对象时，请使用此属性。


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

当此特性应用于接口定义它将为专用标记的默认构造函数。 这意味着可以仍实例化此类的对象在内部从扩展文件，但它只是不是您的类的用户易于访问。

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

使用此属性的类型定义，绑定 OBJECTIVE-C 的类别并公开这些作为C#扩展方法，以镜像 Objective C 的方式公开的功能。

类别是用于扩展的类中可用方法和属性集的 OBJECTIVE-C 的机制。   在实践中，它们用于扩展的基类的功能 (例如`NSObject`) 特定框架中的链接时 (例如`UIKit`)，使其方法可用，但仅当新的框架中的链接。   在某些其他情况下，它们用于组织在类中的功能的功能。   它们是在精神到类似C#扩展方法。

这是什么类别将如下所示在 Objective c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上面的示例中找到一个库，它将会扩展的实例上`UIView`使用方法`makeBackgroundRed`。

若要将这些绑定，可以使用[ `[Category]` ](#CategoryAttribute)接口定义的属性。   使用时[ `[Category]` ](#CategoryAttribute)属性的含义[ `[BaseType]` ](#BaseTypeAttribute)属性更改被用来指定要扩展到所要扩展的类型的基类。

下面演示如何`UIView`绑定扩展并将其转换为C#扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述操作将创建`MyUIViewExtension`包含的类`MakeBackgroundRed`扩展方法。   这意味着您现在可以调用`MakeBackgroundRed`任何`UIView`子类中，为您提供相同的功能上 Objective-c。

在某些情况下您会发现**静态**类别内的成员如下面的示例中所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

这将导致**正确**类别C#接口定义：

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

这是不正确因为使用`BoolMethod`需要的实例扩展`FooObject`但绑定 ObjC**静态**扩展，这是由于如何的这一事实的副作用C#实现的扩展方法.

使用上面定义的唯一方法是通过以下难以理解的代码：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免此问题的建议是内联`BoolMethod`内的定义`FooObject`接口定义本身，这将允许您像其目的是调用此扩展`FooObject.BoolMethod (range)`。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

我们将发出警告 (BI1117)，我们发现只要[ `[Static]` ](#StaticAttribute)内的成员[ `[Category]` ](#CategoryAttribute)定义。 如果你确实想要具有[ `[Static]` ](#StaticAttribute)成员内的你[ `[Category]` ](#CategoryAttribute)定义可以通过使用提示警告`[Category (allowStaticMembers: true)]`或通过修饰您的成员或[`[Category]` ](#CategoryAttribute)接口定义与[ `[Internal]` ](#InternalAttribute)。

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

此特性应用于类时它只是将生成一个静态类，其中一个不是派生自`NSObject`，因此[ `[BaseType]` ](#BaseTypeAttribute)忽略属性。 静态类用于承载您想要公开的 C 公共变量。

例如：

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

将生成C#类的以下 API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>协议/模型定义

协议实现通常使用模型。
它们不同，在运行时将仅向注册 Objective C 实际上已被覆盖的方法。
否则，该方法将不会注册。

一般情况下，这意味着，当子类具有已标记的类`ModelAttribute`，不应调用基方法。   调用该方法将引发异常，应上重写任何方法在子类中实现的全部行为。

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

默认情况下，是一种协议的一部分的成员不是必需的。 这样，用户可以创建一个子类`Model`只是从中的类派生的对象C#和重写仅他们所关心的方法。 有时 Objective C 合同中要求用户提供此方法的实现 (这些标记与`@required`指令 Objective C 中)。 在这些情况下，您应标记与这些方法`[Abstract]`属性。

`[Abstract]`特性可以应用于方法或属性，并导致要生成的成员标记为抽象类和类是一个抽象类的生成器。

下面是取自 Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

指定要返回的模型方法中，如果用户未提供的模型对象中的此特定方法的方法的默认值

语法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在下面的虚部委托类的`Camera`类中，我们提供`ShouldUploadToServer`会上的作为属性公开`Camera`类。 如果的用户`Camera`类未显式设置为 true 或 false 可以响应的 lambda 的值，返回的默认值在此情况下将为 false，我们在中指定的值`DefaultValue`属性：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果用户设置一个处理程序虚类中，将忽略此值：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

另请参阅： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

语法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

此属性时提供的 model 类上返回一个值的方法上将指示要返回的指定参数的值，如果用户未提供自己的方法或 lambda 的生成器。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上面的案例如果的用户`NSAnimation`类选择使用的任何C#事件/属性，并且未设置`NSAnimation.ComputeAnimationCurve`对方法或 lambda，则返回值将是进度参数中传递的值。

另请参阅： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有时它也不能公开事件或委派属性从 Model 类到宿主类，以便添加此属性将指示要避免使用它修饰的任何方法生成的生成器。

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

模型方法的返回值来设置要使用的委托签名名称中使用此特性。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

与上面的定义，生成器将生成下面的公共声明：

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

此属性用于允许要更改的主机类中生成的属性名称的生成器。 有时它很有用时 FooDelegate 类方法的名称对于委托类中，有意义，但将会作为属性在主机类中不正常。

这也是非常有用 （和所需） 时有两个或更多意义的重载方法保留它们的命名方式是 FooDelegate 类中但你想要将它们显示在具有更好的给定名称的主机类。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

与上面的定义，生成器将生成以下公共主机类中的声明：

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

采用多个参数的事件 （Objective C 中的约定是委托类中的第一个参数是发件人对象的实例） 必须提供您要用于生成的 EventArgs 类，是的名称。 这通过`[EventArgs]`属性中的方法声明上你`Model`类。

例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述声明将生成`UIImagePickerImagePickedEventArgs`类，该类派生自 EventArgs 并包这两个参数，`UIImage`和`NSDictionary`。 生成器将生成此：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它公开中的以下`UIImagePickerController`类：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

此属性用于允许要更改的事件或在类中生成的属性名称的生成器。 有时它时，模型类方法的名称有意义的模型类，但看起来奇数原始类中作为事件或属性。

例如，`UIWebView`使用下面这段从`UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述公开`LoadingFinished`中的方法作为`UIWebViewDelegate`，但`LoadFinished`与事件挂接到`UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

当应用`[Model]`协定 API，在运行时中的类型定义的属性将生成特殊代码，将仅向类中的方法呈现调用，如果用户已覆盖类中的方法。 此属性通常应用于 OBJECTIVE-C 的委托类包装的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定在模型上的方法不提供默认返回值。

这适用于 Objective C 运行时通过响应`false`Objective C 运行时请求以确定是否此类中实现指定的选择器。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另请参阅： [ `[DefaultValue]` ](#DefaultValueAttribute)， [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>协议

Objective C 协议概念中实际上不存在C#。 协议则类似于C#接口，但它们的差异在于不是所有的方法，必须由采用它的类实现一种协议中声明的属性。 而是一些方法和属性是可选的。

某些协议通常用作模型的类，应使用绑定的那些[ `[Model]` ](#ModelAttribute)属性。

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
```

已合并 Xamarin.iOS 从 7.0 开始新的和改进的协议绑定功能。  包含任何定义`[Protocol]`属性实际上会生成极大提高使用协议的方法的三个支持类：

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

**类实现**提供完整的抽象类可以重写的各个方法并获取完整的类型安全。 但由于C#不支持多重继承，有可能需要不同的基类，但仍想要实现接口的方案。

这就是生成**接口定义**传入。  它是具有来自协议的所有所需的方法的接口。  这允许开发人员想要实现您的协议只是实现该接口。  在运行时将自动注册为采用协议类型。

请注意，仅列出了所需的方法和接口确实公开了可选的方法。   这意味着采用协议的类将获得完整的类型检查所需的方法，但将不得不求助于弱类型化 （手动使用导出特性和匹配签名） 的可选的协议方法。

为了更方便地使用使用协议的 API，绑定工具还将生成一个扩展方法类公开的所有可选方法。   这意味着，只要您正在使用 API，则您将能够将视作具有所有方法的协议。

如果你想要在你的 API 中使用的协议定义，您需要在你的 API 定义中编写主干空接口。   如果你想要使用 MyProtocol API 中，您需要执行此操作：

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

### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成接口

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

### <a name="protocol-inlining"></a>内联的协议

虽然绑定已声明为采用一种协议的现有 Objective C 类型时，您将需要内联协议直接。 若要执行此操作，只是将声明您的协议而无需任何接口[ `[BaseType]` ](#BaseTypeAttribute)属性，并列出在你的接口的基接口列表中的协议。

示例:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```


## <a name="member-definitions"></a>成员定义

在本部分中的特性应用于类型的单个成员： 属性和方法声明。


### <a name="alignattribute"></a>AlignAttribute

用于指定属性的返回类型的对齐值。 某些属性采用必须某些边界上对齐的地址的指针 (在 Xamarin.iOS 出现这种情况如某些`GLKBaseEffect`属性必须是 16 字节的对齐)。 您可以使用此属性来修饰 getter，并使用对齐值。 这通常与一起使用`OpenTK.Vector4`和`OpenTK.Matrix4`类型时与 Objective C Api 集成。

示例:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]`属性仅限于 iOS 5、 外观 manager 引入。

`[Appearance]`特性可以应用于任何方法或属性的参与`UIAppearance`框架。 当此特性应用于方法或类中的属性时，它将直接绑定生成器来创建用于设置样式的此类的所有实例或符合特定条件的实例的强类型化外观类。

示例:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

上述将生成下面的代码在 UIToolbar 中：

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

使用`[AutoReleaseAttribute]`上的方法和属性来包装方法调用中的方法为`NSAutoReleasePool`。

Objective C 中有一些方法，返回值添加到默认`NSAutoReleasePool`。 默认情况下，这些将转到你的线程`NSAutoReleasePool`，但由于 Xamarin.iOS 还保留对您的对象的引用，前提是在托管的对象存在，可能不想要保留的额外引用`NSAutoReleasePool`这仅获取耗尽之前你的线程返回控制转移到下一个线程，或您回到主循环。

此属性为例应用于大量属性 (例如`UIImage.FromFile`)，它返回已添加到默认对象`NSAutoReleasePool`。 没有此属性，只要你的线程未返回到主循环的控制会保留映像。 如果你的线程是某种形式的始终处于活动状态的后台程序下载程序并等待工作，映像会永远不会被释放出来。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`用于强制创建的托管类型，即使返回的非托管的对象的绑定定义中所述的类型不匹配。

标头中描述的类型与本机方法的返回的类型不匹配时，这很有用，例如需要从以下的 Objective C 定义`NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它能够清楚地表明它将返回`NSURLSessionDownloadTask`实例，但尚未它**返回** `NSURLSessionTask`，这是超类并因此不转换为`NSURLSessionDownloadTask`。 由于我们是在类型安全的上下文中`InvalidCastException`将发生这种情况。

若要符合标头说明并避免`InvalidCastException`，则`[ForcedTypeAttribute]`使用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`还接受一个名为的布尔值`Owns`也就是说`false`默认情况下`[ForcedType (owns: true)]`。 拥有使用参数来遵循[所有权策略](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)有关**核心基础**对象。

`[ForcedTypeAttribute]`仅适用于参数、 属性和返回值。

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`允许绑定`NSNumber`，`NSValue`并`NSString`（枚举） 到了更多准确C#类型。 该属性可用于创建更好、 更准确，基于本机 API 的.NET API。

您可以修饰 （上返回值） 的方法、 参数和属性与`BindAs`。 唯一限制是，您的成员**不得**位于内部`[Protocol]`或[ `[Model]` ](#ModelAttribute)接口。

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

当前支持的封装类型包括：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

以下C#数据类型支持从/到封装`NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

以下C#数据类型支持从/到封装`NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* 枚举

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) 适用于使用 conjuntion[枚举作为后盾 NSString 常量](#enum-attributes)以便您可以创建更好的.NET API，例如：

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

将输出：

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

我们将处理`enum`  <->  `NSString`仅当提供的枚举类型的转换[ `[BindAs]` ](#BindAsAttribute)是[受 NSString 常量](#enum-attributes)。

#### <a name="arrays"></a>数组

[`[BindAs]`](#BindAsAttribute) 此外支持数组的任何受支持的类型，你可以作为示例的以下 API 定义：

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

将输出：

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

`rects`参数将封装到`NSArray`，其中包含`NSValue`为每个`CGRect`并反过来将获取一个数组`CAScroll?`其中使用所返回的值创建`NSArray`包含`NSStrings`。

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

`[Bind]`属性具有两个使用同一种时应用于方法或属性声明中和另一个应用于单个 getter 或 setter 的属性中时。

当使用一种方法或属性、 影响`[Bind]`属性是以生成方法的调用指定的选择器。 但结果生成的方法不用修饰[ `[Export]` ](#ExportAttribute)属性，这意味着，它不能参与方法重写。 这通常与结合使用`[Target]`实现 Objective C 的扩展方法的属性。

例如：

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Getter 或 setter，在使用时`[Bind]`属性用于更改时生成的属性的 getter 和 setter 的 Objective C 选择器名称推断代码生成器的默认值。 默认情况下，当您标记具有名称属性时`fooBar`，生成器将生成`fooBar`导出的 getter 和`setFooBar:`资源库。 在少数情况下，Objective C 不遵循此约定，它们通常将 getter 名称为`isFooBar`。
将使用此属性以通知此生成器。

例如：

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

仅适用于 Xamarin.iOS 6.3 及更高版本。

此特性可以应用于采用完成处理程序作为其最后一个参数的方法。

可以使用`[Async]`其最后一个参数是一个回调方法的属性。  绑定生成器时将此应用于方法时，将生成该方法带有后缀的版本`Async`。  如果回调不带任何参数，返回值将是`Task`，如果回调采用参数，则结果将是`Task<T>`。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

以下将生成此异步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回调采用多个参数，则应设置`ResultType`或`ResultTypeName`来指定所需的生成将保存的所有属性的类型名称。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

以下将生成此异步方法，其中`FileLoading`包含属性，以同时访问`files`和`byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回调的最后一个参数是`NSError`，然后生成`Async`方法将检查，如果值不为 null，并且如果这种情况下，生成的异步方法将设置的任务异常。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述生成以下异步方法：

```csharp
Task<string> UploadAsync (string file);
```

和错误时，生成任务将具有设置为异常`NSErrorException`包装生成`NSError`。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

使用此属性返回的值指定`Task`对象。   此参数接受一个现有类型，因此它需要一个您的核心 api 定义中定义。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用此属性返回的值指定`Task`对象。   此参数采用的所需的类型名称，生成器将生成一系列属性，一个用于回调会获取每个参数。

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

使用此属性自定义生成的异步方法的名称。   默认值是使用方法的名称并将文本"Async"追加，可以使用此更改此默认设置。

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

此特性应用于字符串参数或字符串属性，并指示要使用的零复制字符串封送处理为此参数，并改为创建一个新 NSString 实例的代码生成器C#字符串。
此属性仅需要对字符串，如果指示要使用零复制字符串封送处理使用的生成器`--zero-copy`命令行选项或设置程序集级别属性`ZeroCopyStringsAttribute`。

这是在属性声明为的 Objective C 中的位置的情况下有必要`retain`或`assign`而不是属性`copy`属性。 这些通常会发生在已错误地"优化"开发人员的第三方库中。 一般情况下，`retain`或`assign``NSString`属性不正确自`NSMutableString`或用户派生的类的`NSString`可能更改的库代码中，略有重大不知情的情况下，字符串的内容应用程序。 通常发生这种情况过早优化。

以下目标 c： 驱动器中显示两个此类属性

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

当应用`[DisposeAttribute]`到一个类，提供代码片段将添加到`Dispose()`类的方法实现。

由于`Dispose`方法将自动生成`bmac-native`并`btouch-native`工具，您需要使用`[Dispose]`属性将在生成一些代码注入`Dispose`方法实现。

例如：

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]`特性用于标记方法或属性会面临 Objective C 运行时。 此属性的绑定工具和实际的 Xamarin.iOS 和 Xamarin.Mac 运行时之间共享。 对于方法，传递的参数是逐字到生成的代码，属性、 getter 和 setter 的导出将生成基于基声明的 (请参阅章节[ `[BindAttribute]` ](#BindAttribute)有关如何更改的信息绑定工具的行为）。

语法：

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

[选择器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)表示的基础 Objective C 方法或属性被绑定的名称。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

此特性用于将 C 全局变量公开为按需加载并向公开的字段C#代码。 通常这需要获取的 C 或 Objective C 中定义的这可能是某些 Api 中使用任一标记或其值是不透明和必须用作的常量值-是由用户代码。

语法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是 C 符号将其与链接。 默认情况下这将从加载的库的命名空间中推断出其名称的类型定义的位置。 如果这不是在其中查找符号的库，则应传递`libraryName`参数。 若要将静态库，使用`__Internal`作为`libraryName`参数。

生成的属性始终是静态的。

使用字段属性标记的属性可以是以下类型之一：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 枚举

不支持资源库[枚举作为后盾 NSString 常量](#enum-attributes)，但它们也可以手动绑定，如果需要。

示例:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

`[Internal]`特性可以应用于方法或属性，它起的标记生成的代码替换`internal`C#关键字使代码只能访问到代码中生成的程序集。 这通常用于隐藏太低级别的 Api 或提供一个非最优的公共 API，你想要提高时或生成器不支持的 api 并需要一些手动编码。

在设计时绑定，通常会隐藏方法或使用此属性的属性并提供其他名称，为方法或属性，然后在你C#互为补充的支持文件中，您将添加公开的强类型化包装器基本功能。

例如：

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然后，在您支持的文件，其中可能有一些类似下面的代码：

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

此特性标记的一个属性，以使用.NET 进行批注的支持字段`[ThreadStatic]`属性。 这是很有用，如果此字段为线程静态变量。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

此属性将使方法支持本机 (OBJECTIVE-C) 异常。
而不是调用`objc_msgSend`直接调用将了解到自定义 trampoline 捕获 ObjectiveC 异常并将它们封送到托管异常。

当前仅有少数`objc_msgSend`支持签名 (您将了解到签名不受支持，如果使用该绑定的应用的本机链接失败且缺少 monotouch_*_objc_msgSend*符号)，但的详细信息可以是添加在请求时。


### <a name="newattribute"></a>NewAttribute

此特性应用于方法和属性，以具有生成器生成`new`关键字声明的前面。

它用于避免编译器警告时相同的方法或属性名称在基类中已存在的子类中引入。

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

您可以将此特性应用于字段具有生成器生成强类型化帮助器 Notifications 类。

此属性可以使用不带参数不执行任何负载的通知，也可以指定`System.Type`引用在 API 定义中，另一个接口通常与名称以"EventArgs"结尾。 生成器会将接口转换类子类`EventArgs`，将包括所有列出的属性。 [ `[Export]` ](#ExportAttribute)属性应在`EventArgs`类，以列出用于查找要提取的值的 Objective C 字典的键的名称。

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
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

你的代码用户然后可以轻松地订阅通知发布到[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)通过使用如下代码：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或设置要观察特定对象。 如果传递`null`到`objectToObserve`此方法的行为就像其其他对等方。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
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

将生成上述`MyScreenChangedEventArgs`类的`ScreenX`并`ScreenY`将提取的数据的属性[NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo)字典使用键`ScreenXKey`和`ScreenYKey`分别并应用正确的转换。 `[ProbePresence]`属性为生成器用来探测如果设置了密钥`UserInfo`，而不是尝试提取的值。 这用于项的状态与的值 （通常为布尔值） 的情况。

这可以编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情况下，没有任何常量与字典上传递的值相关联。  Apple 有时会使用公共符号常量和有时会使用字符串常量。  默认情况下[ `[Export]` ](#ExportAttribute)属性中将提供`EventArgs`类将使用指定的名称作为公共符号来查找在运行时。  如果这不是这种情况，并改为它应为字符串常量查找，然后将传递`ArgumentSemantic.Assign`Export 特性的值。

**Xamarin.iOS 8.4 中的新增功能**

有时，通知将开始不带任何参数，因此使用[ `[Notification]` ](#NotificationAttribute)不是可接受的参数。  但有时，将引入的通知的参数。  若要支持这种情况下，该特性可以多次应用。

如果你正在开发一个绑定，并且你想要避免破坏现有的用户代码，您需要阅读从现有通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

为列出的通知属性两次，此类的版本：

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

这应用到属性时它会标记为允许值的属性`null`要分配给它。 此值仅对于引用类型有效。

当这应用到它表示指定的参数可以为 null，并且应传递执行任何检查方法签名中的参数`null`值。

如果引用类型不具有此属性，该绑定工具将生成然后再将它传递到 Objective C 所赋的值的检查，并且将生成的检查，将引发`ArgumentNullException`分配的值是如果`null`。

例如：

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

此属性用于指示应将此特定方法的绑定标记具有绑定生成器`override`关键字。

### <a name="presnippetattribute"></a>PreSnippetAttribute

可以使用此属性将已验证的输入的参数后，但在之前的代码调用到 Objective-c。 要插入一些代码注入

示例:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

此属性可用于插入之前的任何参数验证生成的方法中插入一些代码。

示例:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

指示要调用此类，以从其提取值的指定的属性的绑定生成器。

此属性通常用于刷新指向保留引用的对象图的引用对象的缓存。 通常就会显示已添加/删除等操作的代码。 使用此方法，以便添加或删除，内部缓存更新，确保元素后我们将对实际使用中的对象的托管的引用。 这可能是因为绑定工具生成给定绑定中的所有引用对象的支持字段。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

在这种情况下，`Dependencies`属性将添加或删除从依赖项后调用`NSOperation`对象，确保我们具有一个图，表示的实际加载的对象，防止内存泄漏以及内存损坏。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

可以使用此属性以注入一段C#源代码，以在代码已调用基础的 OBJECTIVE-C 方法后插入

示例:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

此特性是应用于返回值，以标记为正在代理对象。 不可以从用户绑定区分某些 Objective C Api 返回的代理对象。 此属性的效果是标记为对象`DirectBinding`对象。 有关在 Xamarin.Mac 中的方案，可以看到[讨论此 bug](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留托管的引用参数或删除对参数的内部引用的生成器。 这用来保留对象引用。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果的值`doAdd`为 true，则该参数添加到`__mt_{0}_var List<NSObject>;`。 其中`{0}`替换为给定`listName`。 您必须在对 API 的补充分部类中声明此支持字段。

有关示例请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

这可以应用于返回类型以指示应调用生成器`Release`在返回之前的对象。 是时才需要这样一种方法提供一个保留对象 （而不是一个 autoreleased 对象，它是最常见的方案）

示例:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外此特性被传播到生成的代码，以便 Xamarin.iOS 运行时知道它必须保留来自此类函数返回到 OBJECTIVE-C 的相应对象。


### <a name="sealedattribute"></a>SealedAttribute

指示要标记为已密封生成的方法的生成器。 如果未指定此属性，默认值是生成虚拟方法 （虚方法、 一个抽象方法或重写，具体取决于如何使用其他属性）。

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

当`[Static]`特性应用于方法或属性，这将生成的静态方法或属性。 如果未指定此属性，然后生成器生成的实例方法或属性。


### <a name="transientattribute"></a>TransientAttribute

使用此属性为标志属性的值是暂时性的也就是说，临时创建的 iOS 但不是生存期较长的对象。 当此特性应用于一个属性时，生成器将不会创建此属性，这意味着托管的类不会保留对对象的引用的支持字段。

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

在设计中的 Xamarin.iOS/Xamarin.Mac 绑定`[Wrap]`属性用于包装具有强类型化对象的弱类型化对象。 发挥作用，大多数都涉及到 OBJECTIVE-C 的委托对象通常声明为类型的这`id`或`NSObject`。 使用 Xamarin.iOS 和 Xamarin.Mac 的约定是公开这些委托或数据源的类型为`NSObject`和命名约定"Weak"+ 所公开的名称。 `id delegate` Objective C 中的属性公开为`NSObject WeakDelegate { get; set; }`API 协定文件中的属性。

但通常分配给此委托的值为的强类型，因此我们面上的强类型，并应用`[Wrap]`属性，这意味着用户可以选择使用弱类型，如果它们需要一些精细控制，或它们需要求助于低级别 tricks，也可以使用强类型化属性的大部分工作。

示例:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

这是用户将如何使用委托的弱类型化版本：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

这是用户将如何使用强类型化的版本，请注意，用户充分利用C#的类型系统，并使用 override 关键字来声明其意图和他无需手动修饰该方法`[Export]`，因为我们完成了该用户的绑定中的工作：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

另一个用途`[Wrap]`属性是为了支持强类型化的方法的版本。  例如：

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

由生成的成员`[Wrap]`不是`virtual`默认情况下，如果您需要`virtual`成员可以设置为`true`可选`isVirtual`参数。

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>参数特性

本部分介绍可应用于方法定义中的参数的属性以及`[NullAttribute]`，适用于作为一个整体的属性。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

此特性应用于参数类型C#委托声明，以通知相关参数符合 Objective C 的联编程序阻止调用约定，并应将它以这种方式封送。

这通常用于定义此类目的 c： 驱动器中的回调

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅：[CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

此特性应用于参数类型C#委托声明相关参数符合调用约定的 C ABI 函数指针，并应封送它以这种方式通知联编程序。

这通常用于定义此类目的 c： 驱动器中的回调

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅：[BlockCallback](#BlockCallback)。

### <a name="params"></a>params

可以使用`[Params]`方法定义以已经有注入"params"定义中的生成器的最后一个数组参数上的属性。   这允许要轻松地为可选参数允许的绑定。

例如，以下定义：

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

允许编写以下代码：

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

这具有附加的优势，它不需要用户以创建仅用于传递元素的数组。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

可以使用`[PlainString]`属性以指示要将字符串作为 C 字符串，而不是传递作为参数传递的绑定生成器的字符串参数的前面`NSString`。

使用大多数 Objective C Api`NSString`参数，但 Api 公开了一些`char *`适用于传递的字符串，而不 API`NSString`变体。
使用`[PlainString]`在这些情况下。

例如，以下的 Objective C 声明：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

应将此类绑定：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示要保留对指定的参数的引用的生成器。 生成器将为此字段提供后备存储，也可以指定一个名称 ( `WrapName`) 来存储处的值。 这可用于保存对，作为参数传递到 Objective C 和当您知道 Objective C 将只保留对象的此副本的托管对象的引用。 例如，API，例如`SetDisplay (SomeObject)`会使用此属性，因为它是可能 SetDisplay 一次可以仅显示一个对象。 如果您需要跟踪 （例如，对于一个类似于堆栈的 API) 的多个对象会使用`[RetainList]`属性。

语法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留托管的引用参数或删除对参数的内部引用的生成器。 这用来保留对象引用。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果的值`doAdd`为 true，则该参数添加到`__mt_{0}_var List<NSObject>`。 其中`{0}`替换为给定`listName`。 您必须在对 API 的补充分部类中声明此支持字段。

有关示例请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

此特性应用于的参数和从 Objective C 到转换时，才使用C#。  在这些转换各种 Objective C`NSObject`参数包装到托管对象的表示形式。

运行时将需要对本机对象的引用，并保留引用，直到对对象的最后一个托管的引用将消失，而 GC 有机会运行。

在少数情况下，非常重要的C#运行时不保留对本机对象的引用。  这有时发生时的基础本机代码已附加到参数的生命周期的特殊行为。  例如： 参数的析构函数将执行一些清理操作，或释放一些宝贵的资源。

此属性通知运行时所需的对象，如有可能从覆盖方法将返回到 Objective C 时释放。

此规则很简单： 如果在运行时必须创建新的托管表示形式从本机对象，然后在函数结束时，将删除本机对象的保留计数，并托管对象的句柄属性将被清除。   这意味着，如果保留对托管对象的引用，该引用将变得无用 （其上调用方法将引发异常）。

如果未创建传递的对象，或如果已有对象的未处理托管表示形式，强制的 dispose 不会发生。 


## <a name="property-attributes"></a>属性特性

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

此属性用于支持其中具有 getter 的属性在基类中引入和可变子类引入了 setter 的 OBJECTIVE-C 的术语。

由于C#不的支持此模型中，类的基类需要具有 setter 和 getter，并且可以使用子类[OverrideAttribute](#OverrideAttribute)。

此属性仅在属性 setter 中使用，用于支持可变惯用语法中 Objective-c。

示例:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enum 特性

映射`NSString`为枚举值的常量是轻松地创建更好的.NET API。 它：

* 允许通过显示为更有用的代码补全**仅**API; 的正确值
* 添加类型安全，不能使用其他`NSString`常量中的不正确的上下文; 和
* 允许隐藏一些常量，使代码补全功能而不会丢失功能显示较短的 API 列表。

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
```

从上面的绑定定义生成器将创建`enum`本身，还将创建`*Extensions`包括两种方法转换方法枚举值之间的静态类型和`NSString`常量。 这意味着即使它们不是 API 的一部分，常量仍可供开发人员。

示例：

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

您可以修饰**一个**具有此特性的枚举值。 这将成为所返回的枚举值未知的常量。

从上面的示例：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果没有枚举值进行了修饰则`NotSupportedException`将引发。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

错误代码会绑定为枚举值。 通常没有为它们的错误域，并不总是易于查找哪一个应用 （或如果即使存在）。

此属性可用于将域时的错误与枚举本身相关联。

示例:

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

然后可以调用扩展方法`GetDomain`获取域常量的任何错误。

### <a name="fieldattribute"></a>FieldAttribute

这是相同的`[Field]`属性的类型中的常量。 它还可在枚举与特定的常量值映射。

一个`null`值可用于指定应返回的枚举值，如果`null``NSString`指定常量。

从上面的示例：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果没有`null`值存在时则`ArgumentNullException`将引发。

## <a name="global-attributes"></a>全局特性

或者使用应用全局特性`[assembly:]`等特性修饰符[ `[LinkWithAttribute]` ](#LinkWithAttribute)也可以是用于任何地方，如[ `[Lion]` ](#SinceAndLionAttributes)和[ `[Since]`](#SinceAndLionAttributes)属性。

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

这是使开发人员可以指定所需重新绑定的库使用而不会迫使库的使用者，若要手动配置 gcc_flags 链接标志和其他 mtouch 参数传递到库的程序集级别属性。

语法：

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

此特性应用于程序集级别，例如，这是什么[CorePlot 绑定](https://github.com/mono/monotouch-bindings/tree/master/CorePlot)使用：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

当你使用`[LinkWith]`属性，指定`libraryName`嵌入到生成的程序集，允许用户发布包含非托管依赖项以及正确使用所需的命令行标志的单个 DLL从 Xamarin.iOS 库。

还有可能不提供`libraryName`，在这种情况下`LinkWith`属性可用于仅指定其他链接器标志：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 构造函数

这些构造函数，可以指定要与链接和嵌入到生成程序集，受支持的库支持的目标和不使用库进行链接所需的任何可选库标志的库。

请注意，`LinkTarget`参数推断的 Xamarin.iOS，不需要进行设置。

示例：

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

`ForceLoad`属性用于确定是否`-force_load`链接标志用于链接本机库。 现在，这始终应为 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

如果要绑定的库在任何框架上有一项硬性要求 (以外`Foundation`并`UIKit`)，应设置`Frameworks`属性设置为包含所需的平台框架的空格分隔列表的字符串。 例如，如果要绑定一个库，需要`CoreGraphics`并`CoreText`，则会将设置`Frameworks`属性设置为`"CoreGraphics CoreText"`。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

设置此属性设置为 true，如果生成可执行文件需要进行编译使用C++而不是默认情况下，这是一个 C 编译器的编译器。 如果要绑定的库用编写使用此C++。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

要绑定的非托管库的名称。 这是具有扩展名".a"的文件，它可以包含多个平台 （如 ARM 和模拟器的 x86） 的对象代码。

检查早期版本的 Xamarin.iOS`LinkTarget`属性来确定平台库支持，但这是现在自动检测和`LinkTarget`属性将被忽略。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字符串提供了绑定作者指定任何其他链接器标志时本机库链接到应用程序需要一种方法。

例如，如果本机库需要 libxml2 和 zlib，您将设置`LinkerFlags`字符串应用到`"-lxml2 -lz"`。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

检查早期版本的 Xamarin.iOS`LinkTarget`属性来确定平台库支持，但这是现在自动检测和`LinkTarget`属性将被忽略。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

设置此属性设置为 true，如果要链接的库需要 GCC 异常处理库 (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

`SmartLink`属性应设置为 true，则让 Xamarin.iOS 确定是否`ForceLoad`为必需。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`属性的工作原理是相同`Frameworks`属性，不同之处在于在链接时，`-weak_framework`说明符传递给 gcc 高级版，每个列出的框架。

`WeakFrameworks` 使库和平台框架针对弱链接的应用程序，以便他们可以选择可以使用它们，如果它们是可用但不是会硬依赖于它们这很有用，如果你的库旨在上添加额外的功能更高版本iOS 的版本。 弱链接的详细信息，请参阅 Apple 的文档上[弱链接](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)。

适合进行弱链接会`Frameworks`喜欢帐户`CoreBluetooth`， `CoreImage`， `GLKit`，`NewsstandKit`和`Twitter`因为它们仅在 iOS 5 中可用。

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) 和 LionAttribute (macOS)

您使用`[Since]`标志 Api 为具有某个特定点在时间中引入的属性。 该属性仅应该用于标记类型和方法可能会导致运行时问题，如果基础的类、 方法或属性不可用。

语法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它应在一般情况下不会应用于枚举、 约束或新的结构因为那些不会导致运行时错误，如果装有操作系统的较旧版本的设备上执行。

当应用于类型的示例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

当应用于新的成员时的示例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`[Lion]`属性应用相同的方式，但与 Lion 引入的类型。 若要使用的原因`[Lion]`而不是在 iOS 中使用的更多特定版本号是主要 OS X 版本会很少发生和容易记住的由其版本号比其代号为操作系统时非常频繁，修订该 iOS

### <a name="adviceattribute"></a>AdviceAttribute

使用此属性可为开发人员提供有关可能会更方便地使用其他 Api 的提示。   例如，如果提供的 API 的强类型化版本，您可以使用此属性在弱类型属性将定向到更好的 API 开发人员。

在文档中显示此属性的信息和工具可以开发，向用户建议提供有关如何改进

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

仅可在 Xamarin.iOS 5.4 和更高版本。

此属性指示生成器的此特定库的绑定 (如果应用使用`[assembly:]`) 或类型应使用快速的零复制字符串封送处理。 此属性等同于传递的命令行选项`--zero-copy`到生成器。

当对字符串使用零副本，生成器有效地使用相同C#为 Objective C 使用且不会产生一个新创建的字符串的字符串`NSString`对象和避免复制将数据从C#字符串应用于Objective C 的字符串。 使用零复制字符串的唯一缺点是，您必须确保任何字符串属性，用于包装碰巧被标记为`retain`或`copy`具有`[DisableZeroCopy]`属性设置。 这是需要，因为零复制字符串的句柄在堆栈上分配和无效时返回的函数。

示例:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

此外可以将应用程序集级别特性和将应用于所有类型的程序集：

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>强类型字典

使用 Xamarin.iOS 8.0 引入了用于轻松创建强类型化类的自动换行的支持`NSDictionaries`。

虽然始终是可以使用[DictionaryContainer](xref:Foundation.DictionaryContainer)数据类型以及手动 API，现在就大大简化，若要执行此操作。  有关详细信息，请参阅[呈现强类型](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

当此特性应用于接口时，生成器将生成具有相同的名称派生自的接口的类[DictionaryContainer](xref:Foundation.DictionaryContainer) ，以及如何启用到强类型在接口中定义的每个属性getter 和 setter 字典。

这将自动生成可以实例化的类从现有`NSDictionary`或创建的新。

此属性接受一个参数，其中包含用于访问在字典上的元素的键的类的名称。   默认情况下该属性的接口中每个属性将查找带有后缀"密钥"的名称的指定类型中的成员。

例如：

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

在上面的示例中，`MyOption`类将生成一个字符串属性，对于`Name`将使用`MyOptionKeys.NameKey`作为要检索的字符串的字典的键。   将使用`MyOptionKeys.AgeKey`作为要检索的字典的键`NSNumber`其中包含 int。

如果你想要使用不同的密钥，可以在如属性，使用 export 特性：

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>强字典类型

中支持以下数据类型`StrongDictionary`定义：

|C#接口类型|`NSDictionary` 存储类型|
|---|---|
|`bool`|`Boolean` 存储在 `NSNumber`|
|枚举值|整数存储在 `NSNumber`|
|`int`|存储在 32 位整数 `NSNumber`|
|`uint`|存储在 32 位无符号的整数 `NSNumber`|
|`nint`|`NSInteger` 存储在 `NSNumber`|
|`nuint`|`NSUInteger` 存储在 `NSNumber`|
|`long`|64 位整数存储在 `NSNumber`|
|`float`|存储为 32 位整数 `NSNumber`|
|`double`|存储为 64 位整数 `NSNumber`|
|`NSObject` 子类|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array`的 `NSObject`|`NSArray`|
|C#`Array`的枚举|`NSArray` 包含`NSNumber`值|
