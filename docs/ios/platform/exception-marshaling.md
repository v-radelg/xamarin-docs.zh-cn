---
title: Xamarin 中的异常封送处理
description: 本文档介绍如何在 Xamarin iOS 应用程序中使用本机和托管异常。 本文讨论了可能发生的问题以及解决这些问题的解决方案。
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 13b88619ccd7d01f32afd5b9332c7dcb426380b0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527978"
---
# <a name="exception-marshaling-in-xamarinios"></a>Xamarin 中的异常封送处理

_Xamarin 包含可帮助响应异常的新事件, 特别是在本机代码中。_

托管代码和目标-C 都支持运行时异常 (try/catch/finally 子句)。

但是, 它们的实现是不同的, 这意味着运行时库 (Mono 运行时和目标 C 运行时库) 必须处理异常, 然后运行用其他语言编写的代码。

本文档介绍了可能出现的问题以及可能的解决方案。

它还包括一个可用于测试不同方案及其解决方案的示例项目, 即[异常封送处理](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)。

## <a name="problem"></a>问题

如果引发异常, 则会出现此问题, 在堆栈展开过程中, 遇到的帧与引发的异常的类型不匹配。

用于 Xamarin 或 Xamarin 的典型示例是当本机 API 引发目标 C 异常时, 如果堆栈展开进程到达托管帧, 则必须以某种方式处理目标 C 异常。

默认操作是不执行任何操作。 对于上述示例, 这意味着允许目标-C 运行时展开托管的帧。 这是有问题的, 因为目标-C 运行时不知道如何展开托管帧;例如, 它不会执行`catch`该`finally`帧中的任何或子句。

### <a name="broken-code"></a>中断的代码

请考虑以下代码示例:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

这将在本机代码中引发 NSInvalidArgumentException:

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

堆栈跟踪将如下所示:

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

框架0-3 是本机帧, 而目标-C 运行时中的 stack 展开器_可以_展开这些帧。 具体而言, 它将执行任何目标 C `@catch`或`@finally`子句。

但是, 目标-C stack 展开器_不_能正确地展开托管帧 (框架 4-6), 因为这些帧将展开, 但不会执行托管异常逻辑。

这意味着通常不能通过以下方式捕获这些异常:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

这是因为目标-C stack 展开器不知道托管`catch`子句, 也不`finally`会执行子句。

当上面的代码示例生效时, 这是因为, 客观-c 有一种方法可以通知出现未处理的目标- [`NSSetUncaughtExceptionHandler`][2]C 异常, 即, xamarin 和 xamarin 使用, 此时将尝试转换任何目标 c 异常托管异常。

## <a name="scenarios"></a>方案

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>方案 1-使用托管 catch 处理程序捕获目标为 C 的异常

在下面的方案中, 可以使用托管`catch`处理程序来捕获目标 C 异常:

1. 引发目标 C 异常。
2. 目标 C 运行时将遍历堆栈 (但不展开堆栈), 并查找可处理异常的`@catch`本机处理程序。
3. 目标-C 运行时找不到`@catch`任何处理程序`NSGetUncaughtExceptionHandler`, 调用, 并调用由 Xamarin/xamarin 安装的处理程序。
4. Xamarin/Xamarin. Mac 的处理程序会将目标 C 异常转换为托管异常, 并引发异常。 由于目标-C 运行时未展开堆栈 (仅遍历堆栈), 因此当前帧与引发目标 C 异常的帧相同。

这里出现了另一个问题, 因为 Mono 运行时不知道如何正确展开目标 C 帧。

当已不捕获 Xamarin 的目标-C 异常回调时, 堆栈如下所示:

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (*)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

此处, 唯一的托管帧是框架 8-10, 但托管异常在帧0中引发。 这意味着 mono 运行时必须展开本机帧 0-7, 这会导致与上述问题等效的问题: 尽管 Mono 运行时将展开本机帧, 但不会执行任何目标 C `@catch`或子句`@finally`.

代码示例:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

不会执行子句, 因为展开此帧的 Mono 运行时并不知道它。 `@finally`

这种情况的一种变化是在托管代码中引发托管异常, 然后在本机帧中展开以转到第`catch`一个托管子句:

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

托管`UIApplication:Main`方法将调用本机`UIApplicationMain`方法, 然后在最终调用托管`AppDelegate:FinishedLaunching`方法之前, iOS 将执行大量的本机代码执行, 当托管异常时, 堆栈上仍有大量本机帧出

```
 0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
 1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
 2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
 5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
 6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
 7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
 8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
 9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
17: FrontBoardServices      -[FBSSerialQueue _performNext]
18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
21: CoreFoundation          __CFRunLoopDoSources0
22: CoreFoundation          __CFRunLoopRun
23: CoreFoundation          CFRunLoopRunSpecific
24: CoreFoundation          CFRunLoopRunInMode
25: UIKit                   -[UIApplication _run]
26: UIKit                   UIApplicationMain
27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])
```

帧0-1 和27-30 是托管的, 而它们之间的所有这些都是本机。 如果通过这些帧展开 Mono, 则不会执行`@catch`目标`@finally` C 或子句。

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>方案 2-无法捕捉目标 C 异常

在下面的方案中,_无法_使用托管`catch`处理程序捕获 o c o o o o o o o o o o o o o o o o o o o o o o o o o o o,

1. 引发目标 C 异常。
2. 目标 C 运行时将遍历堆栈 (但不展开堆栈), 并查找可处理异常的`@catch`本机处理程序。
3. 目标 C 运行时查找`@catch`处理程序, 展开堆栈, 并开始`@catch`执行处理程序。

这种情况通常出现在 Xamarin iOS 应用程序中, 因为在主线程上通常会出现如下代码:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

这意味着, 在主线程上绝不会真正导致未处理的目标 C 异常, 因此永远不会调用将 o o o o o o o o o o o C o o o o o o o o o o o o

在早期 macOS 版本上调试 Xamarin 应用程序时, 这一点也很常见, 因为在早期版本上, 由于检查调试器中的大部分 UI 对象将尝试获取与执行平台上不存在的选择器相对应的属性 (因为 Xamarin 支持更高的 macOS 版本)。 调用此类选择器将`NSInvalidArgumentException`引发 (不可识别的选择器发送到 ... "), 这最终会导致进程崩溃。

总而言之, 使目标 C 运行时或 Mono 运行时展开帧无法进行处理可能导致未定义的行为, 例如, 崩溃、内存泄漏和其他类型的不可预测 (mis) 行为。

## <a name="solution"></a>解决方案

在 Xamarin 10 和 Xamarin. Mac 2.10 中, 我们添加了对任何托管本机边界上都捕获托管和目标-C 异常的支持, 并添加了用于将该异常转换为其他类型的支持。

在伪代码中, 它如下所示:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

Objc_msgSend 的 P/Invoke 被截获, 而这称为:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

对于反向, 还会执行类似操作 (将托管异常封送到目标-C 异常)。

托管本机边界上的捕获异常并不收费, 因此默认情况下不会始终启用:

- Xamarin/tvOS: 在模拟器中启用了目标-C 例外。
- 在所有情况下都强制使用 watchOS: 截获, 因为允许目标-C 运行时展开托管帧会使垃圾回收器混乱, 并使其挂起或崩溃。
- Xamarin: 针对调试版本启用了目标-C 例外。

"[生成时标志](#build_time_flags)" 部分说明了如何在默认情况下启用拦截。

## <a name="events"></a>事件

截获异常后, 将引发两个新事件: `Runtime.MarshalManagedException`和。 `Runtime.MarshalObjectiveCException`

这两个事件都`EventArgs`被传递一个对象, 该对象包含引发的原始`Exception`异常 (属性) 和`ExceptionMode`一个用于定义应如何封送处理异常的属性。

可以在事件处理程序中更改属性,以根据处理程序中执行的任何自定义处理更改行为。`ExceptionMode` 一个示例是在发生特定异常时中止进程。

`ExceptionMode`更改属性将应用于单个事件, 而不会影响将来截获的任何异常。

可用的模式如下:

- `Default`：默认值因平台而异。 如果 GC `ThrowObjectiveCException`处于协作模式 (watchOS) `UnwindNativeCode` , 则为; 否则为 (iOS/watchOS/macOS)。 将来可能会更改默认值。
- `UnwindNativeCode`：这是以前的 (未定义) 行为。 如果在协作模式 (这是 watchOS 上的唯一选项, 这是 watchOS 上的唯一选项) 中使用 GC, 则此选项不可用, 但它是所有其他平台的默认选项。
- `ThrowObjectiveCException`：将托管异常转换为目标 C 异常, 并引发目标 C 异常。 这是 watchOS 上的默认值。
- `Abort`：中止进程。
- `Disable`：禁用异常拦截, 因此在事件处理程序中设置此值并不合理, 但一旦引发事件, 则无法将其禁用。 在任何情况下, 如果设置, 它将表现`UnwindNativeCode`为。

对于向托管代码封送目标为 C 的异常, 可以使用以下模式:

- `Default`：默认值因平台而异。 如果 GC `ThrowManagedException`处于协作模式 (watchOS) `UnwindManagedCode` , 则为; 否则为 (iOS/tvOS/macOS)。 将来可能会更改默认值。
- `UnwindManagedCode`：这是以前的 (未定义) 行为。 如果在协作模式 (这是 watchOS 上的唯一有效 GC 模式, 这是 watchOS 上的无效选项) 中使用 GC, 则此选项不可用, 但它是所有其他平台的默认选项。
- `ThrowManagedException`：将目标为 C 的异常转换为托管异常, 并引发托管异常。 这是 watchOS 上的默认值。
- `Abort`：中止进程。
- `Disable`:D isables 异常截获, 因此在事件处理程序中设置此值并不合理, 但一旦引发事件, 则无法将其禁用。 在任何情况下, 如果设置, 它将中止进程。

因此, 若要查看每次封送异常, 可以执行以下操作:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>生成时标志

可以将以下选项传递给**mtouch** (适用于 xamarin iOS 应用) 和**mmp** (适用于 xamarin), 这会确定是否启用了异常拦截, 并设置了应发生的默认操作:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

除了之外`ExceptionMode` `MarshalManagedException` `MarshalObjectiveCException` , 这些值与传递给和事件的值相同。 `disable`

选项将主要禁用拦截, 但当不添加任何执行开销时, 我们仍会截获异常。 `disable` 对于这些异常, 仍会引发封送处理事件, 默认模式为执行平台的默认模式。

## <a name="limitations"></a>限制

在尝试捕获目标 C 异常时, `objc_msgSend`只会截获对系列函数的 P/invoke。 这意味着, 对于另一个 C 函数的 P/Invoke, 后者随后会引发任何目标为 C 的异常, 仍将会遇到旧的和未定义的行为 (将来可能会改进)。

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>相关链接

- [异常封送处理 (示例)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
