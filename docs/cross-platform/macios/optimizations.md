---
title: 生成优化
description: 本文档介绍在 Xamarin.iOS 和 Xamarin.Mac 应用的生成时应用的各种优化。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: f1aa805b9b7a16ad1e8af573cf4170f885eb0197
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261194"
---
# <a name="build-optimizations"></a>生成优化

本文档介绍在 Xamarin.iOS 和 Xamarin.Mac 应用的生成时应用的各种优化。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>删除 UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

删除对[UIApplication.EnsureUIThread] [ 1] （适用于 Xamarin.iOS) 或`NSApplication.EnsureUIThread`（适用于 Xamarin.Mac)。

这种优化会更改以下类型的代码：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

为以下：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

默认情况下启用为发布生成。

可以通过传递重写默认行为`--optimize=[+|-]remove-uithread-checks`mtouch/mmp 到。

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>Inline IntPtr.Size

内嵌元素的常量值的`IntPtr.Size`根据目标平台。

这种优化会更改以下类型的代码：

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

为以下 （为生成项目时在 64 位平台）：

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

默认情况下，如果针对同一个体系结构，或平台程序集 (**Xamarin.iOS.dll**， **Xamarin.TVOS.dll**， **Xamarin.WatchOS.dll**或**Xamarin.Mac.dll**)。

如果面向多个体系结构，此优化将创建为 32 位版本和 64 位版本的应用程序中，不同的程序集，这两个版本将需要包含在应用中，有效地增加而不是减小最终应用程序大小它。

可以通过传递重写默认行为`--optimize=[+|-]inline-intptr-size`mtouch/mmp 到。

## <a name="inline-nsobjectisdirectbinding"></a>内联 NSObject.IsDirectBinding

`NSObject.IsDirectBinding` 是实例属性，用于确定指示特定实例的包装器类型是否为 (包装器类型是映射到本机类型的托管的类型; 对于实例托管`UIKit.UIView`类型映射到本机`UIView`类型-相反是用户类型在这种情况下`class MyUIView : UIKit.UIView`是用户类型)。

需要知道的值`IsDirectBinding`时调用 Objective C，因为值可确定哪个版本的`objc_msgSend`使用。

对于下面的代码：

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

我们可以确定这`UIView.SomeProperty`的值`IsDirectBinding`不是常量并且不能为内联：

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

但是，就可以查看在应用中的所有类型，并确定没有类型继承自`NSUrl`，并因此是安全的内联`IsDirectBinding`为常量值`true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

具体而言，此优化将更改以下类型的代码 (这是绑定代码`NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

为以下 (当它可确定不是否存在的任何子类`NSUrl`应用中):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它是默认情况下始终启用适用于 Xamarin.iOS，并始终默认情况下禁用适用于 Xamarin.Mac （因为它是可以动态加载程序集在 Xamarin.Mac 中的，不能以确定特定类永远不会创建子类）。

可以通过传递重写默认行为`--optimize=[+|-]inline-isdirectbinding`mtouch/mmp 到。

## <a name="inline-runtimearch"></a>内联 Runtime.Arch

这种优化会更改以下类型的代码：

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

为以下 （时生成的设备）：

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

始终启用默认情况下适用于 Xamarin.iOS （它不是适用于 Xamarin.Mac 可用）。

可以通过传递重写默认行为`--optimize=[+|-]inline-runtime-arch`mtouch 到。

## <a name="dead-code-elimination"></a>死码消除

这种优化会更改以下类型的代码：

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

到：

```csharp
Console.WriteLine ("Doing this");
```

它还将评估常量比较，如下：

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

并确定该表达式`8 == 8`是始终 true，并减少到：

```csharp
Console.WriteLine ("Doing this");
```

这是功能强大的优化与内联的优化，一起使用时，因为它可以转换以下类型的代码 (这是绑定代码`NFCIso15693ReadMultipleBlocksConfiguration.Range`):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

到此 (对于 64 位设备，在生成时和时还可以确保有没有`NFCIso15693ReadMultipleBlocksConfiguration`子类的应用中):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 编译器已能够执行消除死代码类似，但在链接器，这意味着，可以看到有多个方法，不使用，并因此可能会删除 （除非使用其他位置） 链接器内部完成这种优化:

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它始终启用默认情况下 （如果启用链接器）。

可以通过传递重写默认行为`--optimize=[+|-]dead-code-elimination`mtouch/mmp 到。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>优化对 BlockLiteral.SetupBlock 的调用

Xamarin.iOS/Mac 运行时需要知道委托的块签名创建托管的 Objective C 块时。 这可能是一个相当高昂的操作。 此优化将在生成时，计算块签名并修改 IL 调用`SetupBlock`改为将作为参数的签名的方法。 执行此操作不需要使用计算在运行时的签名。

基准显示，这加快了原来的 10 到 15 调用一个块。

它将转换以下[代码](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

到：

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

使用静态注册机构 （在 Xamarin.iOS 中静态注册机构的设备生成的默认情况下启用，并在默认情况下，对于版本启用静态注册机构的 Xamarin.Mac 中生成） 时，它是默认情况下启用。

可以通过传递重写默认行为`--optimize=[+|-]blockliteral-setupblock`mtouch/mmp 到。

## <a name="optimize-support-for-protocols"></a>优化对协议的支持

Xamarin.iOS/Mac 运行时需要有关如何将托管类型实现 Objective C 协议的信息。 此信息存储在接口 （和这些接口的属性），这不是非常有效的格式，也不是链接器友好。

例如，这些接口存储中的所有协议成员有关的信息`[ProtocolMember]`属性，它首先包含对这些成员的参数类型的引用。 这意味着只需实现此类接口将使链接器保留在该接口中使用的所有类型，即使对于的可选成员应用永远不会调用或实现。

这种优化会使将任何所需的信息存储在使用既轻松又快捷在运行时查找内存少的有效格式的静态注册机构。

它还将介绍链接器，它不一定需要保留这些接口和任何相关的属性。

此优化需要链接器和静态注册机构才可用。

在 Xamarin.iOS 上这种优化启用链接器和静态注册机构时默认启用。

在 Xamarin.Mac 这种优化是永远不会启用默认情况下，由于 Xamarin.Mac 支持动态加载程序集和这些程序集可能不具有已生成时已知的 （并因此没有进行优化）。

可以通过传递重写默认行为`--optimize=-register-protocols`mtouch/mmp 到。

## <a name="remove-the-dynamic-registrar"></a>删除动态注册机构

Xamarin.iOS 和 Xamarin.Mac 运行时包括对支持[注册托管的类型](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/)Objective C 运行时使用。 它可以在生成时或在运行时 （或部分在生成时和在运行时的其余部分），但如果它完全执行此操作在生成时，这意味着可以删除在运行时执行的支持代码。 这会导致应用程序大小显著缩短特别适用于较小的应用，例如扩展或 watchOS 应用。

此优化需要静态注册机构和链接器才可用。

链接器将尝试确定它是否可以安全地删除动态注册机构，以及如果是这样会尝试将其删除。

由于 Xamarin.Mac 支持动态加载程序集在运行时 （这不知道在生成时），就无法确定在生成时，这是否是安全优化。 这意味着永远不会为 Xamarin.Mac 应用的默认情况下启用此优化。

可以通过传递重写默认行为`--optimize=[+|-]remove-dynamic-registrar`mtouch/mmp 到。

如果重写默认值来删除动态注册机构，链接器将发出警告，如果它检测到的是不安全的 （但仍会删除动态注册机构）。

## <a name="inline-runtimedynamicregistrationsupported"></a>内联 Runtime.DynamicRegistrationSupported

内嵌元素值的`Runtime.DynamicRegistrationSupported`确定在生成时。

如果删除动态注册机构 (请参阅[删除动态注册机构](#remove-the-dynamic-registrar)优化)，这是一个常量`false`值，否则它是一个常量`true`值。

这种优化会更改以下类型的代码：

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

为以下时删除动态注册机构：

```csharp
throw new Exception ("dynamic registration is not supported");
```

为以下时动态注册机构不会删除：

```csharp
Console.WriteLine ("do something");
```

此优化需要链接器启用，并且仅应用于具有方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它始终启用默认情况下 （如果启用链接器）。

可以通过传递重写默认行为`--optimize=[+|-]inline-dynamic-registration-supported`mtouch/mmp 到。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>预计算的方法来创建托管的委托的 Objective C 块

当 Objective C 调用选择器，采用一个块作为参数，并托管的代码然后已经重写该方法中，Xamarin.iOS / Xamarin.Mac 运行时需要为该块创建委托。

将包含由绑定生成器生成的绑定代码`[BlockProxy]`特性，指定具有类型`Create`可以执行此操作的方法。

给定以下 Objective C 代码：

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

以及以下绑定代码：

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

生成器将生成：

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }
    
    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...
        
    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;
        
        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }
    
    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

当调用 Objective C `[ObjCBlockTester callClassCallback]`，Xamarin.iOS / Xamarin.Mac 运行时将查看`[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]`参数上的属性。 它将查找`Create`方法对该类型，并调用该方法，以创建委托。

此优化将查找`Create`方法在生成时和静态注册机构将生成用于查找的方法在运行时使用的元数据令牌，而不使用属性和反射 （这是快得多，还允许链接器代码若要删除相应的运行时代码，从而使应用更小）。

如果找不到 m m p/mtouch`Create`方法，然后将会显示 MT4174/MM4174 警告，并查找将在运行时执行。
最可能的原因手动编写且没有所需的绑定代码`[BlockProxy]`属性。

此优化需要静态注册机构以进行启用。

它始终启用默认情况下 （只要启用了静态注册机构）。

可以通过传递重写默认行为`--optimize=[+|-]static-delegate-to-block-lookup`mtouch/mmp 到。
