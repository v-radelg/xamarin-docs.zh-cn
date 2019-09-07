---
title: 绑定类型参考指南
description: 本参考指南介绍了在创建C#到目标 C 库的绑定时需要了解的各种属性和概念。
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: ef94c90cec11c374b24ddfb159674adb468e72de
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765791"
---
# <a name="binding-types-reference-guide"></a>绑定类型参考指南

本文档介绍可用于批注 API 协定文件以驱动绑定和生成的代码的属性列表

Xamarin 和 Xamarin. Mac API 协定C#主要作为定义目标 C 代码的显示方式的接口定义。 C# 此过程涉及混合使用接口声明以及 API 协定可能需要的基本类型定义。 有关绑定类型的简介，请参阅[绑定目标-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)的配套指南。

## <a name="type-definitions"></a>类型定义

语法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

协定定义中的每个接口都具有[`[BaseType]`](#BaseTypeAttribute)为生成的对象声明基类型的特性。 在以上声明中， `MyType`将C#生成一个绑定到名`MyType`为的目标 C 类型的类类型。

如果你使用接口继承语法指定 typename 之后（在上面`Protocol1`的示例中）指定任何类型， `Protocol2`则这些接口的内容将会内联，就好像它们是的协定的`MyType`一部分一样。
类型采用协议的 Xamarin 表面的方式是将协议中声明的所有方法和属性内联到类型本身中。

下面演示了如何在 Xamarin iOS 协定中定义`UITextField`的目标 C 声明：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

将像下面这样编写C# API 协定：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以通过将其他特性应用到接口并配置[`[BaseType]`](#BaseTypeAttribute)特性，来控制代码生成的其他许多方面。

### <a name="generating-events"></a>生成事件

Xamarin 和 Xamarin. Mac API 设计的一项功能是将目标-C 委托类映射为C#事件和回调。 用户可以选择基于每个实例，无论它们是否想要采用目标 c 编程模式，都可以分配给属性（例如`Delegate`实现目标 c 运行时将调用的各种方法的类的实例，或选择样式C#的事件和属性。

让我们看一下如何使用目标 C 模型的一个示例：

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

在上面的示例中，您可以看到，我们已选择覆盖两个方法，一个是滚动事件发生的通知，另一个是应返回布尔值`scrollView`的回调，指示是否应滚动到top 或 not。

使用C#该模型，你的库用户可以使用C#事件语法或属性语法来侦听要返回值的回调的通知。

这就是使用C# lambda 的相同功能的代码：

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

由于事件不返回值（它们具有 void 返回类型），因此可以连接多个副本。 不是事件，而是具有具有此签名的类型`UIScrollViewCondition`的属性： `ShouldScrollToTop`

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它将返回`bool`一个值，在这种情况下，lambda 语法允许我们只`MakeDecision`从函数返回值。

绑定生成器支持生成事件和属性，这些事件和属性将`UIScrollView`与其关联`UIScrollViewDelegate`的类（也称为模型类），这是通过使用`Events`和[`[BaseType]`](#BaseTypeAttribute) `Delegates`批注您的定义来实现的。参数（如下所述）。 除了[`[BaseType]`](#BaseTypeAttribute)使用这些参数进行注释外，还必须将更多组件通知生成器。

对于采用多个参数的事件（在目标-C 中，约定是委托类中的第一个参数是发送方对象的实例），您必须为生成`EventArgs`的类提供要使用的名称。 这是通过模型类中[`[EventArgs]`](#EventArgsAttribute)的方法声明上的属性实现的。 例如:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上面的声明将生成一个`UIImagePickerImagePickedEventArgs`派生自`EventArgs`的类，并`UIImage`打包这两个`NSDictionary`参数：和。 生成器将生成：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它将在`UIImagePickerController`类中公开以下内容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

返回值的模型方法的绑定方式有所不同。 这些要求既需要生成C#的委托的名称（方法的签名），也需要返回默认值以防止用户自行提供实现。 例如， `ShouldScrollToTop`定义如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述操作将创建一个`UIScrollViewCondition`具有上面显示的签名的委托，如果用户未提供实现，则返回值将为 true。

除了[`[DefaultValue]`](#DefaultValueAttribute)特性之外，还可以[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)使用指示生成器的特性返回调用[`[NoDefaultValue]`](#NoDefaultValueAttribute)中指定参数的值，或指定指示生成器没有默认值的参数负值.

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

使用`Name`属性可以控制此类型在目标 C 环境中将绑定到的名称。 这通常用于为C#类型指定符合 .NET Framework 设计准则的名称，但映射到不遵循该约定的目标-C 中的名称。

例如，在下面的示例中，我们将目标 C `NSURLConnection`类型映射`NSUrlConnection`到，因为 .NET Framework 设计准则使用 "url" 而不是 "url"：

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名称将用作绑定中生成`[Register]`的特性的值。 如果`Name`未指定，则在生成的输出中使用类型的短名称作为`[Register]`属性的值。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. 事件和 BaseType。委托

这些属性用于驱动生成的C#类中的样式生成事件。 它们用于将给定类与目标 C 委托类进行链接。 在许多情况下，类会使用委托类发送通知和事件。 例如`BarcodeScanner` ，会有一个伴生`BardodeScannerDelegate`类。 类通常具有一个`Delegate`要为其分配实例的`BarcodeScannerDelegate`属性，而这种情况下，你可能希望向用户提供类似的C#样式事件接口，在这种情况下，你将使用`BarcodeScanner` `Events`属性的`Delegates`和属性。 [`[BaseType]`](#BaseTypeAttribute)

这些属性始终设置在一起，并且必须具有相同的元素数，并且保持同步。对于`Delegates`要包装的每个弱类型的委托，该数组包含一个字符串， `Events`数组包含要与其关联的每个类型的一个类型。

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

如果在创建此类的新实例时应用此特性，则将保留该对象的实例，直到调用了被调用的方法`KeepRefUntil` 。 当您不希望用户保留对对象的引用以使用您的代码时，这有助于提高 Api 的可用性。 此属性的值是`Delegate`类中方法的名称，因此您必须将此属性`Events`与和`Delegates`属性结合使用。

下面的示例演示如何`UIActionSheet`在 Xamarin 中使用此方法：

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

<a name="DesignatedDefaultCtorAttribute" />

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

将此特性应用于接口定义时，它将在映射`[DesignatedInitializer]` `init`到选择器的默认（生成）构造函数上生成属性。

<a name="DisableDefaultCtorAttribute" />

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

将此特性应用于接口定义时，它会阻止生成器生成默认构造函数。

如果需要使用类中的其他构造函数之一初始化对象，请使用此特性。

<a name="PrivateDefaultCtorAttribute" />

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

将此特性应用于接口定义时，它会将默认构造函数标记为私有。 这意味着你仍可以从扩展文件内部实例化此类的对象，但你的类的用户将无法访问它。

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

对类型定义使用此属性来绑定目标 C 类别，并将其公开为C#扩展方法，以便在目标 c 公开功能的方式中进行镜像。

类别是一个目标 C 机制，用于扩展类中可用的一组方法和属性。   在实践中，当在中`NSObject` `UIKit`链接特定框架时（例如），它们用于扩展基类的功能，从而使其方法可用，但只有在新框架已链接的情况下才可用。   在某些其他情况下，它们用于按功能组织类中的功能。   它们在本质上与C#扩展方法相似。

这是一种类别在目标-C 中的外观：

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上面的示例位于将`UIView`使用方法`makeBackgroundRed`扩展的实例的库中。

若要绑定这些[`[Category]`](#CategoryAttribute)属性，可以在接口定义上使用特性。   使用[`[Category]`](#CategoryAttribute)属性时， [`[BaseType]`](#BaseTypeAttribute)特性的含义会从用于指定要扩展的基类，并将更改为要扩展的类型。

下面演示了如何绑定`UIView`扩展并将其转换为C#扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上面的将创建`MyUIViewExtension`一个`MakeBackgroundRed`包含扩展方法的类。   这意味着，你现在可以调用`MakeBackgroundRed`任何`UIView`子类，为你提供与目标-C 相同的功能。

在某些情况下，你会在类别中找到**静态**成员，如下面的示例中所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

这将导致类C#接口定义**不正确**：

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

这是不正确的，因为`BoolMethod`若要使用扩展，你`FooObject`需要的是实例，但要绑定 ObjC**静态**扩展，这是因为如何C#实现扩展方法的副作用。

使用上述定义的唯一方法是使用以下不足的代码：

```csharp
(null as FooObject).BoolMethod (range);
```

避免这种情况的建议是将`BoolMethod`定义`FooObject`内联到接口定义本身中，这样就可以像预期`FooObject.BoolMethod (range)`那样调用此扩展。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

当我们在[`[Static]`](#StaticAttribute) [`[Category]`](#CategoryAttribute)定义中找到成员时，我们将发出警告（BI1117）。 如果你确实想要在[`[Static]`](#StaticAttribute) [`[Category]`](#CategoryAttribute)定义中包含成员，则可以通过使用或通过`[Category (allowStaticMembers: true)]`使用[`[Internal]`](#InternalAttribute)修饰成员或[`[Category]`](#CategoryAttribute)接口定义来使警告静音。

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

将此特性应用于类时，它只会生成一个不是从`NSObject`派生的静态类，因此该[`[BaseType]`](#BaseTypeAttribute)属性将被忽略。 静态类用于托管要公开的 C 公共变量。

例如：

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

将使用以下C# API 生成类：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>协议/模型定义

模型通常由协议实现使用。
它们的不同之处在于，运行时仅向目标-C 注册实际已被覆盖的方法。
否则，将不注册该方法。

这通常意味着，当你为已使用标记的类提供`ModelAttribute`子类时，不应调用基方法。   调用该方法将引发异常，您应该在您的子类上对您重写的任何方法实现整个行为。

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

默认情况下，作为协议的一部分的成员不是必需的。 这允许用户仅从中`Model` C#的类派生，并只覆盖他们关心的方法来创建对象的子类。 有时，目标-c 协定要求用户提供此方法的实现（这些方法使用目标-C 中的`@required`指令进行标记）。 在这些情况下，应用`[Abstract]`特性标记这些方法。

`[Abstract]`特性可以应用于方法或属性，并使生成器将生成的成员标记为抽象，并使该类成为抽象类。

下面是从 Xamarin 获取的：

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

如果用户未在模型对象中为此特定方法提供方法，则指定模型方法返回的默认值。

语法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在`Camera`类的以下虚委托类中，我们提供了一个`ShouldUploadToServer`将`Camera`作为类的属性公开的。 如果`Camera`类的用户未将值显式设置为可响应 true 或 false 的 lambda，则在这种情况下，默认值将为 false，这是`DefaultValue`在特性中指定的值：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果用户在虚类中设置处理程序，则将忽略此值：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

请参见： [`[NoDefaultValue]`](#NoDefaultValueAttribute)、 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

语法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

当在一个返回模型类上值的方法上提供此特性时，如果用户未提供其自己的方法或 lambda，将指示生成器返回指定参数的值。

示例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上述情况下，如果`NSAnimation`类的用户选择使用任意C#事件/属性，但未设置`NSAnimation.ComputeAnimationCurve`为方法或 lambda，则返回值将是在进度参数中传递的值。

另请参阅[`[NoDefaultValue]`](#NoDefaultValueAttribute)：、[`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有时，不会将事件类中的事件或委托属性公开到主机类，因此添加此属性将指示生成器避免生成使用它修饰的任何方法。

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

此属性用于返回值的模型方法，以设置要使用的委托签名的名称。

示例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

对于上述定义，生成器将生成以下公共声明：

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

此属性用于允许生成器更改在宿主类中生成的属性的名称。 当 FooDelegate 类方法的名称对委托类有意义时，有时会很有用，但在宿主类中将以属性的形式显示为奇数。

如果有两个或多个重载方法使其在 FooDelegate 类中保持不变，但你想要在具有更好的给定名称的主机类中公开这些方法，则这也是非常有用的。

示例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

对于上述定义，生成器将在 host 类中生成以下公共声明：

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

对于采用多个参数的事件（在目标-C 中，约定是委托类中的第一个参数是发送方对象的实例），您必须为生成的 EventArgs 类提供所需的名称。 这是通过`Model`类中`[EventArgs]`的方法声明上的特性来完成的。

例如:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上面的声明将生成一个`UIImagePickerImagePickedEventArgs`派生自 EventArgs 的类，并打包这两个`UIImage`参数： `NSDictionary`和。 生成器将生成：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它将在`UIImagePickerController`类中公开以下内容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

此属性用于允许生成器更改在类中生成的事件或属性的名称。 当 Model 类方法的名称对模型类有用时，有时会很有用，但在源类中看起来像是事件或属性。

例如， `UIWebView`使用`UIWebViewDelegate`中的以下位：

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

以上将公开`LoadingFinished`为中的方法`UIWebViewDelegate`，但`LoadFinished`作为在中`UIWebView`挂钩到的事件：

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

当你将`[Model]`特性应用于协定 API 中的类型定义时，运行时将生成特殊代码，仅当用户覆盖了类中的方法时，才会向类中的方法进行的调用。 此特性通常应用于包装目标-C 委托类的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定模型上的方法不提供默认的返回值。

这适用于目标 c 运行时，它通过`false`响应目标 c 运行时请求来确定是否在此类中实现了指定的选择器。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另请参阅[`[DefaultValue]`](#DefaultValueAttribute)：、[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>协议

目标-C 协议概念并不真正存在于中C#。 协议与C#接口类似，但不同之处在于协议中声明的所有方法和属性都必须由采用它的类来实现。 相反，某些方法和属性是可选的。

某些协议通常用作模型类，它们应使用[`[Model]`](#ModelAttribute)属性进行绑定。

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

从 Xamarin 7.0 开始，已合并了新的和改进的协议绑定功能。  包含该`[Protocol]`属性的任何定义实际上将生成三个支持类，这些类可大大提高你使用协议的方式：

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

**类实现**提供了一个完整的抽象类，您可以重写的各个方法，并获得完整的类型安全。 但由于C#不支持多个继承，有些情况下您可能需要不同的基类，但仍要实现接口。

这是生成的**接口定义**传入的位置。  它是一个接口，它具有协议中所有必需的方法。  这样，开发人员就可以实现协议来仅实现接口。  运行时将自动将类型注册为采用协议。

请注意，接口仅列出所需的方法，并公开可选方法。   这意味着采用协议的类将对所需的方法获得完全类型检查，但必须使用可选的协议方法的弱类型（手动使用导出属性和匹配签名）。

为了便于使用使用协议的 API，绑定工具还将生成一个扩展方法类，该类公开所有可选方法。   这意味着只要你使用 API，就能将协议视为具有所有方法。

如果要在 API 中使用协议定义，则需要在 API 定义中编写主干空接口。   如果要在 API 中使用 MyProtocol，则需要执行以下操作：

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

需要使用以上方法，因为在绑定时`IMyProtocol`不存在，这就是需要提供空接口的原因。

### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成的接口

每次实现为协议生成的接口之一时，如下所示：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

接口方法的实现会自动以适当的名称导出，因此它等效于：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

接口是隐式实现还是显式实现，这并不重要。

### <a name="protocol-inlining"></a>协议内联

绑定已被声明为采用协议的现有目标 C 类型时，需要直接内联协议。 要执行此操作，只需将协议声明为不带[`[BaseType]`](#BaseTypeAttribute)任何属性的接口，并在接口的基接口列表中列出该协议。

示例：

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

本节中的属性将应用于类型的各个成员：属性和方法声明。

### <a name="alignattribute"></a>AlignAttribute

用于指定属性返回类型的对齐值。 某些属性采用指向必须在特定边界上对齐的地址的指针（在 Xamarin 中，这种情况发生时`GLKBaseEffect` ，某些属性必须是16字节对齐的属性）。 您可以使用此属性来修饰 getter，并使用对齐值。 与目标 C api 集成时`OpenTK.Vector4` ， `OpenTK.Matrix4`这通常与和类型一起使用。

示例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

此`[Appearance]`属性仅限在引入外观管理器的 iOS 5 中。

特性可应用于参与`UIAppearance`框架的任何方法或属性。 `[Appearance]` 将此特性应用于类中的方法或属性时，它将指示绑定生成器创建一个强类型的外观类，该类用于为此类的所有实例或匹配特定条件的实例建立样式。

示例：

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

上述代码将在 UIToolbar 中生成以下代码：

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute （Xamarin 5.4）

使用方法和属性将方法调用包装`NSAutoReleasePool`在中的方法。 `[AutoReleaseAttribute]`

在目标中，有一些方法返回添加到默认`NSAutoReleasePool`值的值。 默认情况下，这些方法会转到`NSAutoReleasePool`你的线程，但由于 Xamarin 也保留对你的对象的引用，只要托管对象存在，你可能不希望`NSAutoReleasePool`在中保留额外的引用，而只会在线程返回对下一个线程的控制，或者返回到主循环。

此特性适用于返回已添加到默认`UIImage.FromFile` `NSAutoReleasePool`的对象的重型属性（例如）。 如果没有此特性，则只要线程未将控制返回到主循环，就会保留图像。 Uf：你的线程是始终处于活动状态并等待工作的某种后台下载程序，将永远不会释放映像。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`用于强制创建托管类型，即使返回的非托管对象与绑定定义中描述的类型不匹配。

如果标头中描述的类型与本机方法的返回类型不匹配，则此方法非常有用，例如，从`NSURLSession`以下目标获取以下目标-C 定义：

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它清楚地表明`NSURLSessionDownloadTask`它将返回一个实例，但它**返回** `NSURLSessionTask`的是一个超超类，因而不能转换为。 `NSURLSessionDownloadTask` 由于我们处于类型安全上下文`InvalidCastException`中，因此将发生。

若要符合标头说明，并避免`InvalidCastException`使用`[ForcedTypeAttribute]` ，则使用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

还接受一个名`Owns`为的布尔值， `false`默认`[ForcedType (owns: true)]`值为。 `[ForcedTypeAttribute]` 拥有参数用于遵循**核心基础**对象的[所有权策略](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)。

`[ForcedTypeAttribute]`仅对参数、属性和返回值有效。

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]` C#允许将和`NSString`（枚举）转换为更准确的类型。 `NSValue` `NSNumber` 特性可用于通过本机 API 创建更好、更准确的 .NET API。

可以用`BindAs`来修饰方法（返回值）、参数和属性。 唯一的限制是，你的成员**不**能位于`[Protocol]`或[`[Model]`](#ModelAttribute)接口内。

例如：

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

输出：

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

在内部`NSNumber` ，我们将`bool?`进行`CGRect` 和 <-> 转换。  <->  `NSValue`

当前支持的封装类型为：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

支持将C#以下数据类型封装为/into `NSValue`：

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect/RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

支持将C#以下数据类型封装为/into `NSNumber`：

* bool
* byte
* double
* 浮点数
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

[`[BindAs]`](#BindAsAttribute)在 conjuntion 中工作，其中包含[由 NSString 常数支持的枚举](#enum-attributes)，因此你可以创建更好的 .net API，例如：

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

输出：

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

仅当提供的`enum`枚举类型[由 NSString 常量支持](#enum-attributes) [`[BindAs]`](#BindAsAttribute)时，才会处理 <->  `NSString`转换。

#### <a name="arrays"></a>数组

[`[BindAs]`](#BindAsAttribute)还支持任何受支持的类型的数组，你可以将以下 API 定义作为示例：

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

输出：

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

`NSArray` `CGRect` `CAScroll?`参数将封装`NSArray`到`NSValue`中，其中每个和返回都将获取一个数组，该数组已使用返回的值创建`rects`包含`NSStrings`。

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

当应用于方法或属性声明时，特性有两个使用一个，另一个在应用于属性中的单个getter或setter时使用。`[Bind]`

当用于方法或属性时， `[Bind]`属性的效果是生成调用指定选择器的方法。 但是，生成的生成的方法不会用[`[Export]`](#ExportAttribute)特性修饰，这意味着它不能参与方法重写。 这通常与`[Target]`用于实现目标 C 扩展方法的属性结合使用。

例如:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

当在 getter 或 setter 中使用时， `[Bind]`该属性用于在生成属性的 getter 和 setter 目标-C 选择器名称时，更改代码生成器推导出的默认值。 默认情况下，在使用名称`fooBar`标记属性时，生成器将为 getter 和`fooBar` `setFooBar:` setter 生成导出。 在少数情况下，目标 C 不遵循此约定，通常它们会将 getter 名称更改为`isFooBar`。
您可以使用此属性来通知生成器此。

例如:

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

仅适用于 Xamarin 6.3 和更高版本。

此特性可应用于采用完成处理程序作为其最后一个参数的方法。

可对其最后`[Async]`一个参数为回调的方法使用特性。  将此应用于方法时，绑定生成器将使用后缀`Async`生成该方法的版本。  如果回调不使用参数，则返回值将为`Task`，如果回调采用参数，则结果将`Task<T>`为。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下面将生成此异步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回调采用多个参数，则应设置`ResultType`或`ResultTypeName`以指定所需的生成类型名称，该名称将包含所有属性。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下面将生成此异步方法，其中`FileLoading`包含用于`files`访问和`byteCount`的属性：

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回调的最后一个参数是`NSError`，则生成`Async`的方法将检查该值是否不为 null，如果是这种情况，则生成的异步方法将设置任务异常。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上面的异步方法如下：

```csharp
Task<string> UploadAsync (string file);
```

出现错误时，生成的任务会将异常设置为`NSErrorException` ，以包装生成`NSError`的。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

使用此属性可指定返回`Task`对象的值。   此参数采用现有类型，因此需要在一个核心 api 定义中定义它。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用此属性可指定返回`Task`对象的值。   此参数采用所需类型名称的名称，生成器将生成一系列属性，每个属性分别对应于回调所需的参数。

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

使用此属性可自定义生成的异步方法的名称。   默认情况下，若要使用方法的名称并追加文本 "Async"，则可以使用此方法来更改此默认值。

<a name="DesignatedInitializerAttribute" />

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

如果将此特性应用于构造函数，它将在最终`[DesignatedInitializer]`平台程序集中生成相同的。 这是为了帮助 IDE 指出应在子类中使用哪个构造函数。

这应映射到的`__attribute__((objc_designated_initializer))`目标-C/clang 使用。

<a name="DisableZeroCopyAttribute" />

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

此特性应用于字符串参数或字符串属性，并指示代码生成器不要对此参数使用零副本字符串封送处理，而是从C#字符串创建新的 NSString 实例。
仅当指示生成器使用`--zero-copy`命令行选项或设置程序集级别的属性`ZeroCopyStringsAttribute`来使用零副本字符串封送处理时，才需要在字符串上使用此属性。

在目标-C `retain`中将属性声明为或`assign`属性而不`copy`是属性时，这是必需的。 这通常发生在由开发人员误 "优化" 的第三方库中。 通常，或`retain` `assign` `NSMutableString`属性是不正确的，因为或的用户派生`NSString`的类可能会改变字符串的内容，而无需了解库代码，但要略微打破`NSString`程序. 通常，这是由于过早的优化而发生的。

下面显示了目标-C 中的两个属性：

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute" />

### <a name="disposeattribute"></a>DisposeAttribute

将应用`[DisposeAttribute]`到类时，将提供一个将添加`Dispose()`到类的方法实现的代码段。

`bmac-native` `[Dispose]`由于和`Dispose` `Dispose`工具会自动生成方法，因此需要使用属性在生成的方法实现中注入一些代码。 `btouch-native`

例如:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]`特性用于标记要向目标 C 运行时公开的方法或属性。 此属性在绑定工具和实际的 Xamarin 和 Xamarin 运行时之间共享。 对于方法，参数逐字传递到生成的代码，对于属性，将基于基声明生成 getter 和 setter 导出（ [`[BindAttribute]`](#BindAttribute)有关如何更改绑定工具的行为的信息，请参阅上的部分）。

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

[选择器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)表示正在绑定的基础目标 C 方法或属性的名称。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

此特性用于将 C 全局变量公开为按需加载并向C#代码公开的字段。 通常需要此方法来获取在 C 或目标-C 中定义的常量的值，这些值可能是某些 Api 中使用的令牌，或者是其值不透明的，并且必须由用户代码按原样使用。

语法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是要链接的 C 符号。 默认情况下，将从定义类型的命名空间中推断出其名称的库中加载此值。 如果这不是在其中查找符号的库，则应该传递`libraryName`参数。 如果要链接静态库，请使用`__Internal` `libraryName`作为参数。

生成的属性始终是静态的。

用字段属性标记的属性可以是以下类型：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 枚举

[NSString 常量](#enum-attributes)支持的枚举不支持 setter，但可以根据需要手动对其进行绑定。

示例：

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

特性可以应用于方法或属性，它具有`internal`用C#关键字标记生成的代码的效果，使得代码只能在生成的程序集中的代码中访问。 `[Internal]` 这通常用于隐藏过于低级别的 Api，或提供要改进的 api，或者提供不受生成器支持的 Api，并且需要进行一些手动编码。

设计绑定时，通常会使用此特性隐藏方法或属性，并为方法或属性提供不同的名称，然后在C#补充支持文件中添加一个强类型包装器，该包装器将公开基础功能。

例如:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然后，在支持文件中，可以使用如下所示的一些代码：

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

此特性标记要使用 .net `[ThreadStatic]`特性批注的属性的支持字段。 如果该字段是线程静态变量，这会很有用。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions （Xamarin 6.0.6）

此属性将使方法支持本机（目标 C）异常。
调用将通过`objc_msgSend`一个自定义 trampoline 来捕获 ObjectiveC 异常并将其封送到托管异常，而不是直接调用。

目前仅支持少量`objc_msgSend`签名（当使用绑定的应用的本机链接失败并缺少 monotouch_ *_objc_msgSend*符号时，你会发现该签名不受支持，但可以在请求时添加更多签名）。

### <a name="newattribute"></a>NewAttribute

此特性应用于方法和属性，使生成器可在声明前面`new`生成关键字。

当在基类中引入了相同的方法或属性名称时，可以使用它来避免编译器警告。

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

可以将此属性应用于字段，以使生成器生成强类型 helper 通知类。

此特性可用于不带有效负载的通知的参数，或者你可以指定`System.Type`引用 API 定义中的另一个接口的，通常名称以 "EventArgs" 结尾。 生成器会将接口转换为子类`EventArgs` ，并将包含列出的所有属性。 应在类中使用特性来列出用于查找目标 C 字典以获取该值的键的名称。 [`[Export]`](#ExportAttribute) `EventArgs`

例如:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上面的代码将使用以下方法生成`MyClass.Notifications`嵌套类：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

然后，你的代码用户可以使用类似如下的代码轻松订阅发布到[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)的通知：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或设置要观察的特定对象。 如果传递`null`到`objectToObserve`此方法的行为与其他对等方的行为相同。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

返回的值`ObserveDidStart`可用于轻松地停止接收通知，如下所示：

```csharp
token.Dispose ();
```

或者，可以调用[NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))并传递令牌。 如果通知中包含参数，应指定一个 helper `EventArgs`接口，如下所示：

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

上面将`MyScreenChangedEventArgs`生成一个`ScreenX`包含和`ScreenY`属性的类，这些属性将使用密钥`ScreenXKey`和`ScreenYKey`分别从[NSNotification](xref:Foundation.NSNotification.UserInfo)字典提取数据，并应用正确的转换。 如果在中设置了该键`UserInfo`，则该特性用于探测，而不是`[ProbePresence]`尝试提取值。 这适用于出现密钥的情况是值（通常用于布尔值）。

这允许你编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情况下，不存在与在字典上传递的值相关联的常量。  Apple 有时使用公共符号常量，有时使用字符串常量。  默认情况下[`[Export]`](#ExportAttribute) ，所提供`EventArgs`类中的属性将使用指定的名称作为公共符号，以便在运行时查找。  如果不是这种情况，则应将其查找为字符串常量，然后将`ArgumentSemantic.Assign`值传递给导出属性。

**Xamarin 8.4 中的新增项**

有时，通知将在没有任何参数的情况下开始使用， [`[Notification]`](#NotificationAttribute)因此可以使用不带参数的。  但有时会引入通知的参数。  若要支持此方案，可以多次应用该属性。

如果你正在开发绑定，并且想要避免破坏现有用户代码，则会从以下内容转换现有通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

列出通知属性两次的版本，如下所示：

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

将此应用于属性时，它会将属性标记为允许将`null`值分配给该属性。 这仅对引用类型有效。

将此应用到方法签名中的参数时，它表示指定的参数可以为 null，并且不应执行任何检查来传递`null`值。

如果引用类型不具有此属性，则绑定工具将在将值传递给目标-C 之前为赋值生成检查，并将生成一个检查，该检查将在指定的`ArgumentNullException`值为时`null`引发。

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

使用此属性指示绑定生成器应使用`override`关键字标记此特定方法的绑定。

### <a name="presnippetattribute"></a>PreSnippetAttribute

您可以使用此属性来注入一些代码，以便在验证输入参数后，但在代码调入目标-C 之前插入。

示例：

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

您可以使用此属性来注入一些代码，以便在生成的方法中验证任何参数之前插入。

示例：

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

指示绑定生成器调用此类中的指定属性以从其获取值。

此属性通常用于刷新缓存，该缓存指向保留引用对象关系图的引用对象。 通常，它会在包含 "添加/删除" 等操作的代码中显示。 使用此方法，以便在添加或删除内部缓存的情况下添加或删除元素，以确保将托管引用保留到实际使用的对象。 这是可能的，因为绑定工具会为给定绑定中的所有引用对象生成一个支持字段。

示例：

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

在这种情况下`Dependencies` ，将在添加或删除`NSOperation`对象中的依赖项后调用属性，确保我们有一个表示实际加载对象的图形，同时防止内存泄漏和内存损坏。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用此属性来注入在C#代码调用基础目标 C 方法之后要插入的一些源代码

示例：

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

此特性应用于返回值，以将其标记为代理对象。 某些目标为 C 的 Api 返回的代理对象无法与用户绑定区分开来。 此特性的作用是将对象标记为`DirectBinding`对象。 对于 Xamarin 中的方案，你可以查看[有关此错误的讨论](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示生成器保留对参数的托管引用或删除对参数的内部引用。 这用于保留引用的对象。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果的`doAdd`值为 true，则将参数添加`__mt_{0}_var List<NSObject>;`到。 其中`{0}`替换为给定`listName`的。 必须将互补分部类中的此支持字段声明为 API。

有关示例，请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute （Xamarin 6.0）

这可以应用于返回类型，以指示生成器应在返回对象`Release`之前对其调用。 仅当一个方法提供了一个保留的对象（而不是 autoreleased 对象，这是最常见的情况）时才需要此方法。

示例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外，此特性将传播到生成的代码，因此，在从这类函数返回到目标时，Xamarin 运行时知道它必须保留对象。

### <a name="sealedattribute"></a>SealedAttribute

指示生成器将生成的方法标记为密封。 如果未指定此属性，则默认值为生成虚拟方法（虚拟方法、抽象方法或重写，具体取决于使用其他属性的方式）。

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

`[Static]`当特性应用于方法或属性时，这会生成一个静态方法或属性。 如果未指定此特性，则生成器将生成实例方法或属性。

### <a name="transientattribute"></a>TransientAttribute

使用此属性来标记值为暂时性的属性，即由 iOS 暂时创建但不长时间的对象。 将此特性应用于某个属性时，生成器不会为此属性创建一个支持字段，这意味着该托管类不保留对该对象的引用。

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

在 Xamarin/xamarin 的设计中， `[Wrap]`特性用于利用强类型化的对象包装弱类型的对象。 大多数情况下都是在目标 C 委托对象中播放，这些对象通常声明为类型`id`或`NSObject`。 Xamarin 和 xamarin 使用的约定是将这些委托或数据源公开为类型`NSObject` ，并使用约定 "弱" + 要公开的名称进行命名。 目标-C 中的`NSObject WeakDelegate { get; set; }` 属性将公开为API协定文件中的属性。`id delegate`

但是，分配给此委托的值通常是一种强类型，因此，我们会介绍强类型并应用`[Wrap]`属性，这意味着，如果用户需要一些精细控制，或者需要使用低级别的 tric，则可以选择使用弱类型ks，或者它们可以使用强类型属性来完成其大部分工作。

示例：

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

这就是用户使用弱类型版本的委托的方式：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

这就是用户使用强类型版本的方法，请注意，用户利用C#的是类型系统，并使用 override 关键字声明其意图，并且不必使用手动修饰方法`[Export]`，因为我们此操作在绑定中适用于用户：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

`[Wrap]`特性的另一个用途是支持强类型版本的方法。  例如:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果对`[Wrap]` `[Category]`使用属性修饰的类型中的方法应用该属性，则需要将作为第一个`This`参数包括在内，因为正在生成扩展方法。 例如：

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

默认`[Wrap]`情况`isVirtual` `true` 下，`virtual`生成的成员不是可以设置为可选参数的成员。`virtual`

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]`还可以直接在属性 getter 和 setter 中使用。
这允许对其进行完全控制，并根据需要调整代码。
例如，请考虑以下使用智能枚举的 API 定义：

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

接口定义：

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>参数特性

本节介绍可应用于方法定义中的参数的特性，以及作为整体应用于属性的`[NullAttribute]` 。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

此特性应用于委托声明中C#的参数类型，以通知联编程序：相关参数符合目标 C 块调用约定，应以这种方式封送处理。

这通常用于在目标-C 中定义如下的回调：

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅：[CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

此特性应用于委托声明中C#的参数类型，以通知联编程序：相关参数符合 C ABI 函数指针调用约定，应以这种方式封送。

这通常用于在目标-C 中定义如下的回调：

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅：[BlockCallback](#BlockCallback)。

### <a name="params"></a>化

您可以使用方法`[Params]`定义的最后一个数组参数上的属性，使生成器在定义中注入 "params"。   这样，绑定就可以轻松地允许可选参数。

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

这种方法的优点在于，它不要求用户创建纯粹用于传递元素的数组。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

可以在字符串参数`[PlainString]`前面使用特性来指示绑定生成器将字符串作为 C 字符串传递，而不是将参数`NSString`作为传递。

大多数目标为 C 的 api `NSString`使用参数，但少数 api `char *`公开用于传递`NSString`字符串的 api，而不是变化形式。
在`[PlainString]`这些情况下使用。

例如，以下目标 C 声明：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

应绑定如下：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示生成器保留对指定参数的引用。 生成器将为此字段提供后备存储，你也可以指定要在其中存储值`WrapName`的名称（）。 这对于保存作为参数传递给目标-C 的托管对象的引用非常有用，但当你知道目标-C 只保留该对象的副本时。 例如，像`SetDisplay (SomeObject)`这样的 API 将使用此属性，因为 SetDisplay 可能每次只能显示一个对象。 如果需要跟踪多个对象（例如，对于类似堆栈的 API），可使用`[RetainList]`属性。

语法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

指示生成器保留对参数的托管引用或删除对参数的内部引用。 这用于保留引用的对象。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果的`doAdd`值为 true，则将参数添加`__mt_{0}_var List<NSObject>`到。 其中`{0}`替换为给定`listName`的。 必须将互补分部类中的此支持字段声明为 API。

有关示例，请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

此特性应用于参数，仅在从目标-C 转换到C#时使用。  在这些转换过程中，各种目标`NSObject` C 参数会包装到对象的托管表示形式中。

运行时将引用本机对象并保留引用，直到最后对该对象的托管引用消失，并且 GC 有机会运行。

在少数情况下， C#运行时不保留对本机对象的引用非常重要。  当基础本机代码已将特殊行为附加到参数的生命周期时，有时会发生这种情况。  例如：参数的析构函数将执行某些清除操作，或释放一些宝贵资源。

此属性可通知运行时在可能的情况下，当从覆盖的方法返回到目标时，您希望释放对象。

此规则非常简单：如果运行时必须从本机对象创建新的托管表示形式，则在函数结束时，将删除本机对象的保留计数，并清除托管对象的 Handle 属性。   这意味着，如果你保存了对托管对象的引用，该引用将变得毫无用处（对它调用方法将引发异常）。

如果未创建传递的对象，或者如果已存在对象的未完成托管表示形式，则不会发生强制释放。 

## <a name="property-attributes"></a>属性特性

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

此特性用于支持目标 C，其中，具有 getter 的属性是在基类中引入的，而可变子类会引入 setter。

由于C#不支持此模型，因此基类需要 setter 和 getter，子类可以使用[OverrideAttribute](#OverrideAttribute)。

此属性仅用于属性资源库，用于支持在目标-C 中的可变用法。

示例：

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

## <a name="enum-attributes"></a>枚举属性

将`NSString`常量映射到枚举值是创建更好的 .net API 的简便方法。 以便

* 允许代码完成更有用，**只**显示 API 的正确值;
* 添加类型安全，不能在错误`NSString`的上下文中使用另一个常数;
* 允许隐藏某些常量，使代码完成在不丢失功能的情况下显示较短的 API 列表。

示例：

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

通过上述绑定定义，生成器将创建`enum`自身，还会创建一个`*Extensions`静态类型，其中包含枚举值和`NSString`常量之间的双向转换方法。 这意味着，即使它们不是 API 的一部分，它们仍可供开发人员使用。

例如：

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

可以用此属性修饰**一个**枚举值。 如果枚举值未知，此值将成为返回的常量。

在上面的示例中：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果未修饰任何枚举值，则`NotSupportedException`将引发。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

错误代码将绑定为枚举值。 通常情况下，它们是一个错误域，并不是很容易找到哪个应用（或即使存在）。

您可以使用此属性将错误域与枚举本身相关联。

示例：

```csharp
[Native]
[ErrorDomain ("AVKitErrorDomain")]
public enum AVKitError : nint {
    None = 0,
    Unknown = -1000,
    PictureInPictureStartFailed = -1001
}
```

然后，可以调用扩展方法`GetDomain`获取任何错误的域常量。

### <a name="fieldattribute"></a>FieldAttribute

这是用于类型`[Field]`中的常量的相同属性。 它还可在枚举中用于映射具有特定常量的值。

值可用于指定如果指定了`null` `NSString`常量，应返回的枚举值。 `null`

在上面的示例中：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果不`null`存在任何值， `ArgumentNullException`则将引发。

## <a name="global-attributes"></a>全局属性

全局`[assembly:]`特性是使用属性修饰符[`[LinkWithAttribute]`](#LinkWithAttribute) （如）应用的，可以在任何地方[`[Lion]`](#SinceAndLionAttributes)使用，如和[`[Since]`](#SinceAndLionAttributes)属性。

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

这是一个程序集级别的属性，它允许开发人员指定重用绑定库所需的链接标志，而不强制库的使用者手动配置传递到库的 gcc_flags 和额外 mtouch 参数。

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

此特性应用于程序集级别，例如，以下是[CorePlot 绑定](https://github.com/mono/monotouch-bindings/tree/master/CorePlot)使用的内容：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

使用`[LinkWith]`属性时，指定`libraryName`的嵌入到生成的程序集中，使用户能够提供包含非托管依赖项的单个 DLL 以及正确使用Xamarin 中的库。

还可以不提供`libraryName`，在这`LinkWith`种情况下，可以使用属性来仅指定其他链接器标志：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 构造函数

这些构造函数允许你指定要与之链接的库，并将其嵌入到生成的程序集中，库支持的目标以及链接到库所需的任何可选库标志。

请注意，由 Xamarin 推导参数，无需设置。`LinkTarget`

例如：

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

此`ForceLoad`属性用于确定`-force_load`链接标志是否用于链接本机库。 现在，此值应始终为 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

如果所绑定的库对任何框架（ `Foundation`和`UIKit`除外）都有硬性要求`Frameworks` ，则应将属性设置为一个字符串，该字符串包含所需平台框架的以空格分隔的列表。 例如，如果要绑定`CoreGraphics`需要和`CoreText`的库`Frameworks` ，则应将属性设置为`"CoreGraphics CoreText"`。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

如果生成的可执行文件需要使用C++编译器而不是默认的（C 编译器）进行编译，则将此属性设置为 true。 如果您绑定的库是用编写的，则使用C++此项。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

要捆绑的非托管库的名称。 这是一个扩展名为 ". a" 的文件，它可以包含多个平台（例如，模拟器的 ARM 和 x86）的对象代码。

早期版本的 Xamarin 已检查`LinkTarget`属性以确定库支持的平台，但这现在是自动检测的，将`LinkTarget`忽略属性。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字符串提供了一种方法，用于在将本机库链接到应用程序时，绑定作者指定所需的任何其他链接器标志。

例如，如果本机库需要 libxml2 和 zlib，则应将`LinkerFlags`字符串设置为。 `"-lxml2 -lz"`

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

早期版本的 Xamarin 已检查`LinkTarget`属性以确定库支持的平台，但这现在是自动检测的，将`LinkTarget`忽略属性。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

如果要链接的库需要 GCC 异常处理库（gcc_eh），请将此属性设置为 true

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

应将`ForceLoad`属性设置为 true，以便 Xamarin iOS 确定是否需要。 `SmartLink`

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

属性的工作方式与`Frameworks`属性的工作方式相同，不同之处在于，在`-weak_framework`链接时，说明符将传递给每个所列出框架的 gcc。 `WeakFrameworks`

`WeakFrameworks`使库和应用程序可以弱链接到平台框架，以便在可以选择使用这些框架和应用程序时可以选择使用它们（如果你的库打算在更高版本上添加额外功能，这很有用）iOS 版本。 有关弱链接的详细信息，请参阅关于[弱链接](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)的 Apple 文档。

适用于弱`Frameworks`链接的候选项类似于帐户、 `CoreImage` `CoreBluetooth`、 `GLKit`、 `NewsstandKit`和`Twitter` ，因为它们仅在 iOS 5 中可用。

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute （iOS）和 LionAttribute （macOS）

使用`[Since]`特性来标记 api，因为在某个时间点引入了 api。 如果基础类、方法或属性不可用，则特性仅用于标记可能导致运行时问题的类型和方法。

语法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

通常不应将其应用于枚举、约束或新结构，因为在使用较早版本的操作系统的设备上执行这些操作时，它们不会导致运行时错误。

应用于类型的示例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

应用于新成员时的示例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`[Lion]`特性的应用方式相同，但适用于 Lion 引入的类型。 使用`[Lion]`与 ios 中使用的版本更具体的原因是，ios 非常频繁地进行修改，而主要 OS X 版本很少发生，并且比 codename 操作系统更容易记住操作系统

### <a name="adviceattribute"></a>AdviceAttribute

使用此属性为开发人员提供有关可能更便于使用的其他 Api 的提示。   例如，如果你提供了一个强类型的 API 版本，则可以在弱类型属性上使用此属性，以将开发人员定向到更好的 API。

此属性中的信息显示在文档和工具中，可以为用户提供有关如何改进的建议

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

这是`[Advice]`特性的专用化子类，可用于提示重写方法的开发人员**需要**对基（重写）方法的调用。

这对应`clang`于[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

仅适用于 Xamarin 5.4 及更高版本。

此属性指示生成器此特定库（如果应用了`[assembly:]`）或类型的绑定应使用快速的零复制字符串封送处理。 此特性等效于将命令行选项`--zero-copy`传递到生成器。

当对字符串使用零副本时，生成器有效地使用C#目标为 C 的字符串，而不会导致创建新`NSString`的对象，并避免将数据从C#字符串复制到目标-C 字符串。 使用零个复制字符串的唯一缺点是您必须确保您换行的任何字符串属性被标记为`retain`或`copy`具有`[DisableZeroCopy]`属性集。 这是必需的，因为零副本字符串的句柄是在堆栈上分配的，在函数返回时无效。

示例：

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

您也可以在程序集级别应用该属性，并且该属性将应用于该程序集的所有类型：

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>强类型字典

借助 Xamarin 8.0，我们引入了对轻松创建包装`NSDictionaries`的强类型类的支持。

尽管始终可以将[DictionaryContainer](xref:Foundation.DictionaryContainer)数据类型与手动 API 结合使用，但现在可以更轻松地执行此操作。  有关详细信息，请参阅[呈现强类型](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

将此特性应用于接口时，生成器将生成一个与从[DictionaryContainer](xref:Foundation.DictionaryContainer)派生的接口同名的类，并将接口中定义的每个属性转换为的强类型 getter 和 setter字典.

这会自动生成一个类，该类可以从现有`NSDictionary`的实例化，也可以是新创建的。

此属性采用一个参数，即包含用于访问字典中的元素的键的类的名称。   默认情况下，具有属性的接口中的每个属性都将查找指定类型中的成员作为后缀为 "Key" 的名称。

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

在上面的示例中， `MyOption`类将生成的字符串`Name`属性，该属性将`MyOptionKeys.NameKey`用作字典中的键来检索字符串。   和将使用`MyOptionKeys.AgeKey`作为字典中的键来`NSNumber`检索包含 int 的。

如果要使用不同的密钥，可以在属性上使用 export 属性，例如：

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

`StrongDictionary`定义中支持以下数据类型：

|C#接口类型|`NSDictionary`存储类型|
|---|---|
|`bool`|`Boolean`存储在`NSNumber`|
|枚举值|存储在中的整数`NSNumber`|
|`int`|存储在中的32位整数`NSNumber`|
|`uint`|存储在中的32位无符号整数`NSNumber`|
|`nint`|`NSInteger`存储在`NSNumber`|
|`nuint`|`NSUInteger`存储在`NSNumber`|
|`long`|存储在中的64位整数`NSNumber`|
|`float`|32位整数存储为`NSNumber`|
|`double`|64位整数存储为`NSNumber`|
|`NSObject`和子类|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array`的`NSObject`|`NSArray`|
|C#`Array`枚举的|`NSArray`包含`NSNumber`值|
