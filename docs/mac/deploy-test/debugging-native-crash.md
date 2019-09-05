---
title: 调试 Xamarin.Mac 应用中的本机故障
description: 本文档描述了如何调试在 Objective-C 运行时中生成的异常。 其中讨论了断言失败、回叫问题、异常浮升等。
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: bb33d9f85dfc1215e91caa20fab3800645e13111
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282177"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>调试 Xamarin.Mac 应用中的本机故障

## <a name="overview"></a>概述

有时，编程错误可能会导致本机 Objective-C 运行时中的故障。 和 C# 异常不同，它们不会指向你可以在代码中查看并修复的特定行。 有时，它们对查找和修复而言微不足道，但其他时候，跟踪它们可能会非常困难。 

我们来查看并演练几个真实的本机故障示例。

## <a name="example-1-assertion-failure"></a>示例 1：断言失败

下面是一个简单的测试应用程序中的前几行故障（信息位于“应用程序输出”  板）：

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

以数字作为前缀的行是本机堆栈跟踪。 从中你可以看到，故障发生在处理行高的 `NSTableView` 中的某处。 然后，`NSAssertionHandler` 会触发 `NSException (objc_exception_throw)`，我们就看到了断言失败：

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

看到断言失败后，很明显，说明 `NSOutlineViewDelegate` 方法返回了负数。 问题如下：

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>示例 2：回调跳至不知何处

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

这个问题跟踪起来更加困难。 当你在托管的堆栈跟踪顶部看到 `at <unknown> <0xffffffff>` 或 `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` 时，建议我们使用已经过垃圾回收的对象尝试执行一些托管代码。 本机堆栈跟踪将 `trackMouse:inRect:ofView:untilMouseUp` 显示为 `NSCell _sendActionFrom`，以便我们在某处处理单击事件，尝试回调 C# 并终止。

一般情况下，这样的错误很难跟踪。 我向按钮处理程序中添加了 `GC.Collect(2)` 以帮助跟踪此问题（强制进行垃圾回收），从而使此问题可重现。 将示例等分一会儿后，删除代码部分直到问题消失，结果发现是[此 bug](https://bugzilla.xamarin.com/show_bug.cgi?id=23378)：

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

`StandardWindowButton()` 返回的 `NSButton` 被回收，即使事件已注册到其中（这就是 bug 所在）。 当我们通过单击调用此事件时，如果按钮已经被垃圾回收，则会发生故障。

虽然这不是此特定问题的根本原因，这样的堆栈跟踪也可能是由 `[Export]` 到 Objective-C 的函数中的错误方法签名引起的。 例如，如果方法预期参数为 `out string` 而你将其键入为 `string`，可能同样会发生故障。

## <a name="example-3-callbacks-and-managed-objects"></a>示例 3：回调和托管对象

当某些事件发生或需要某些数据执行任务时，许多 Cocoa API 会涉及被库“回调”，这会为你提供响应的机会。 虽然你主要考虑 Delegate  和 DataSource  模式，但是有许多以这种方式运行的 API。 例如，当你替代 `NSView` 的方法，然后将其插入可视化树时，你会期待 AppKit 在发生某些事件时对你进行回调。

在大多数情况下，当仍然可以回调这些回调的托管对象目标时，Xamarin.Mac 会正确地避免回收它们。 但是，捆绑中只有很少的 bug 可以中断这一操作。 发生这种情况时，你会看到类似下面的令人不快的故障：

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

如果突然出现这一性质的 bug 时，此指南会帮助你跟踪它们、正确地报告以便将其修复，并在那之前在代码中解决它们。

### <a name="locating"></a>查找

在大部分出现此性质的 bug 的情况下，主要症状是本机故障，通常是一些与 `mono_sigsegv_signal_handler` 或堆栈的顶框架中的 `_sigtrap` 类似的内容。 Cocoa 正在尝试回调到 C# 代码中，这会命中垃圾回收的对象并发生故障。 但是，并不是每个有这些符号的故障都是由此类绑定问题引起的，你需要进行其他深入探索来确认这就是问题所在。 

跟踪这些 bug 的困难之处在于，它们只在垃圾回收已经处理问题对象之后  才发生。 如果你相信自己已经命中了一个这种 bug，请在启动顺序中的某处添加以下代码：

```csharp
new System.Threading.Thread (() => 
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start (); 
```

这会强制应用程序每秒钟运行一次垃圾回收器。 重新运行应用程序，并尝试重现 bug。 如果故障立即发生或持续发生（而不是随机发生），则你位于正确的跟踪中。

### <a name="reporting"></a>报表

下一步是向 Xamarin 报告问题，从而修复绑定以便将来发布。 如果你是商业或企业许可证持有者，请打开票证，位于： 

[http://xamarin.com/support](http://xamarin.com/support)

或者，请搜索现有问题：

- 查看 [Xamarin.Mac 论坛](https://forums.xamarin.com/categories/mac)
- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues)
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。 

请尽可能多地包含以下内容：

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用  。 
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。   

### <a name="working-around"></a>解决问题

跟踪到问题后，使用解决办法修补问题，直到可以修复的捆绑变得简单。 目的在于，通过保存开放引用，避免错误处理的对象（View  、Delegate  、DataSource  ）退出内存。

对于只有单个对象实例的简单情况，请更改以下代码：

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

若要使用如下静态变量：

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

在可以创建多个实例的情况中，可以使用静态 `HashSet`：

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

修复捆绑并升级至包含修复的 Xamarin.Mac 版本后，可以删除解决办法代码。

## <a name="exception-bubbling-and-objective-c"></a>异常冒泡和 Objective-C

绝不应该允许 C# 异常将托管代码“转义”到调用的 Objective-C 方法。 如果允许，结果会处于未定义状态，但通常会涉及故障。 一般情况下，我们会执行所有可能的操作，使本机和托管故障的有用信息冒泡，以帮助你快速解决问题。

在没有技术原因就陷入困境的情况下，设置基础结构来捕获每个托管/本机边界处的托管异常十分昂贵，还有许多  在很多常规操作中都会发生的转换。 很多操作（尤其是涉及 UI 线程的操作）必须快速结束，否则，应用会不连续，并出现不可接受的性能特征。 很多回调都会执行不太可能出现引发的简单操作，所以，在这些情况下，这一开销昂贵且不必要。

因此，我们不会为你设置这些尝试/捕获。 在代码执行不常用操作（除了返回布尔值或简单的数学外）的地方，你可以尝试自行捕获。 

