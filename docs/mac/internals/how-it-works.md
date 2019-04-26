---
title: Xamarin.Mac 的工作原理
description: 本文档介绍 Xamarin.Mac 的内部工作原理。 具体而言，它会查看构造函数、 内存管理，领先于实时编译和注册机构。
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/25/2017
ms.openlocfilehash: 0635e110cb2aa7bc00234d3d06df57e0fd6f966e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033824"
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的工作原理

大多数情况下，开发人员也无需担心如何内部"神奇"的 Xamarin.Mac，但是，有一个大致的内容实质上的工作原理与这两种解释现有文档中将如何帮助了解C#可重用功能区和调试时出现的问题。

在 Xamarin.Mac 应用程序桥接两个领域：包含本机类的实例基于 Objective C 运行时 (`NSString`，`NSApplication`等)，并且没有C#包含的实例的运行时托管类 (`System.String`，`HttpClient`等)。 在这两个领域之间 Xamarin.Mac 创建双向桥，以便应用可以调用 Objective C 中的方法 （选择器） (如`NSApplication.Init`) 和 Objective C 可以调用应用程序的C#方法后 （如应用程序委托的方法）。 一般情况下，到 OBJECTIVE-C 的调用通过以透明方式处理**P/Invoke**和 Xamarin 提供了一些运行时代码。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>公开C#类 / Objective C 的方法

但是，适用于 OBJECTIVE-C 的回调到应用程序的C#对象，它们需要 Objective C 可以理解的方式公开。 这是通过`Register`和`Export`属性。 请参见以下示例：

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

在此示例中，OBJECTIVE-C 运行时现在知道有关名为的类`MyClass`与选择器称为`init`和`run`。

在大多数情况下，这是实现详细信息，开发人员可以忽略，因为大多数回调应用接收将通过重写的方法上`base`类 (如`AppDelegate`， `Delegates`， `DataSources`) 或在**操作**传递到 Api。 在这些情况下，所有`Export`属性不需要在C#代码。

## <a name="constructor-runthrough"></a>构造函数浏览

在许多情况下，开发人员需要公开应用程序的C#到 OBJECTIVE-C 运行时以便可以从位置，例如何时进行实例化的类构造 API 调用在情节提要或 XIB 文件。 下面是在 Xamarin.Mac 应用中使用的最常见的五个构造函数：

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

一般情况下，开发人员应将保留`IntPtr`并`NSCoder`构造函数时创建某些类型如自定义生成`NSViews`单独。 如果 Xamarin.Mac 需要调用这些构造函数之一，以响应 Objective C 运行时请求而删除了它，应用将在本机代码崩溃，并且可能会难以找出确切问题。

## <a name="memory-management-and-cycles"></a>内存管理和周期

在 Xamarin.Mac 中的内存管理是在许多方面与 Xamarin.iOS 非常相似。 它也是一个复杂的主题，其中一个超出本文的讨论范围。 请阅读[内存和性能最佳实践](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>继续操作的实时编译

通常情况下，在生成时不.NET 应用程序编译到机器代码，改为将它们编译为中间层调用获取的 IL 代码_中实时_在应用启动时 (JIT) 编译为机器代码。

JIT 编译所花费的 mono 运行时的时间此机器代码可能会降低 Xamarin.Mac 应用的发布最多 20%，因为需要必要的机器代码，生成的时间。

由于 Apple 在 iOS 上施加的限制，JIT 编译的 IL 代码不是可用于 Xamarin.iOS。 因此，所有的 Xamarin.iOS 应用程序均已满_时间预先_(AOT) 编译为机器代码，在生成周期。

Xamarin.Mac 新增 AOT 的能力的 IL 代码在应用生成周期中，就像 Xamarin.iOS 可以。 使用 Xamarin.Mac_混合_AOT 编译所需的机器代码，大部分的方法，但允许运行时来编译所需的 trampolines 和灵活地继续支持 Reflection.Emit （和其他使用案例的案例当前适用于 Xamarin.Mac）。

有 AOT 有助于 Xamarin.Mac 应用的两个主要方面：

- **更好的"本机"崩溃日志**-如果 Xamarin.Mac 应用程序崩溃时在本机代码中，这是常见情况进行无效 Cocoa api 调用时 (例如发送`null`到不接受它的方法)、 本机崩溃 jit 的日志帧是难以进行分析。 由于 JIT 帧没有调试信息，将有多行，偏移量为十六进制和装卸台上等任何线索。 AOT 生成"实际"命名的帧，并跟踪是更易于阅读。 这也意味着 Xamarin.Mac 应用程序将更好地与交互本机工具如**lldb**并**Instruments**。
- **更好地启动时间性能**-对于大型 Xamarin.Mac 应用程序，多个第二个启动时间，JIT 编译的所有代码可能需要很长时间。 AOT 执行此操作，会预先。

### <a name="enabling-aot-compilation"></a>启用 AOT 编译

在 Xamarin.Mac 中双击启用 AOT**项目名称**中**解决方案资源管理器**，导航到**Mac 生成**并添加`--aot:[options]`到**其他 mmp 参数：** 字段 (其中`[options]`是一个或多个选项来控制 AOT 类型，请参阅下文)。 例如：

![向其他 mmp 参数添加 AOT](how-it-works-images/aot01.png "添加 AOT 到其他 mmp 参数")

> [!IMPORTANT]
> 启用 AOT 编译可以大幅提高生成时，有时多达几分钟，但它可以提高应用程序启动时间平均为 20%。 因此，AOT 编译应仅在上启用**版本**Xamarin.Mac 应用的生成。

### <a name="aot-compilation-options"></a>Aot 编译选项

有几个不同的选项可启用 Xamarin.Mac 应用上的 AOT 编译时进行调整：

- `none` -无 AOT 编译。 此为默认设置。
- `all` -AOT 编译 MonoBundle 中的每个程序集。
- `core` -AOT 编译`Xamarin.Mac`，`System`和`mscorlib`程序集。
- `sdk` -AOT 编译`Xamarin.Mac`和基类库 (BCL) 程序集。
- `|hybrid` -添加到上述选项之一启用混合 AOT，它允许 IL 去除，但将导致编译时间更长。
- `+` -包括 AOT 编译单个文件。
- `-` -从 AOT 编译删除单个文件。

例如，`--aot:all,-MyAssembly.dll`会启用所有 MonoBundle 中的程序集上的 AOT 编译_除_`MyAssembly.dll`并`--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`会启用混合，包括代码 AOT`MyOtherAssembly.dll`和不包括`mscorlib.dll`.

## <a name="partial-static-registrar"></a>部分静态注册机构

开发的 Xamarin.Mac 应用程序，则可能会满足开发期限到重要之间完成更改并对其进行测试的时间降至最低。 策略如模块化的代码库和单元测试可帮助减少编译时间，因为它们降低了应用程序将需要昂贵的完全重新生成的次数。

此外，和新 Xamarin.Mac_部分的静态注册机构_（如率先开发的 Xamarin.iOS） 可以大大降低在 Xamarin.Mac 应用的启动时间**调试**配置。 了解如何使用部分的静态注册机构可以即塞出几乎 5 倍的调试启动将需要一些背景信息注册机构的是，不同之处是静态和动态，之间，以及此"部分静态"版本。

### <a name="about-the-registrar"></a>有关在注册机构

实质上的任何 Xamarin.Mac 应用程序存在于 Apple 和 OBJECTIVE-C 运行时的 Cocoa 框架。 生成此"本机 world"和"托管的 world"的之间的桥梁C#是 Xamarin.Mac 的主要职责。 此任务的一部分由注册机构，在内部执行`NSApplication.Init ()`方法。 这是任何使用中 Xamarin.Mac 的 Cocoa Api 要求的原因之一`NSApplication.Init`首次调用。

注册机构的工作就是通知 Objective C 运行时的应用程序是否存在C#类的派生类中，如`NSApplicationDelegate`， `NSView`， `NSWindow`，并`NSObject`。 这要求在应用中的所有类型，以确定什么需要注册，并且每个类型设置为报表上的哪些元素进行扫描。

此扫描通过**动态**，在使用反射，应用程序启动时或**静态**，作为生成时间步骤。 当选择注册类型，开发人员应注意以下：

- 静态注册可以显著减少启动时间，但可能会降低生成时间显著 （通常多个双精度调试生成时）。 这将为默认**版本**配置生成。
- 动态注册延迟整个这项工作，直到应用程序启动，将跳过代码生成，但这一附加工作可以创建明显的暂停 （至少 2 秒），在应用程序启动。 这一点尤为明显在调试配置版本，其默认值为动态注册和其反射较慢。

部分 Xamarin.iOS 8.13 中首次引入的静态注册为开发人员提供了这两个选项的最佳。 通过预先计算的每个元素中的注册信息`Xamarin.Mac.dll`和传送此信息与静态库 （即只需在生成时链接到） 中的 Xamarin.Mac，Microsoft 已消除了大部分的动态反射时间注册机构时不会影响生成时间。

### <a name="enabling-the-partial-static-registrar"></a>启用部分静态注册机构

部分静态注册机构在 Xamarin.Mac 中启用了通过双击**项目名称**中**解决方案资源管理器**，导航到**Mac 生成**和添加`--registrar:static`到**其他 mmp 参数：** 字段。 例如：

![将部分静态注册机构添加到其他 mmp 参数](how-it-works-images/psr01.png "将部分静态注册机构添加到其他 mmp 参数")

## <a name="additional-resources"></a>其他资源

下面是一些更详细的解释各功能的内部工作方式：

- [Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)
- [注册器](~/ios/internals/registrar.md)
- [适用于 iOS 和 OS X 的 Xamarin Unified API](~/cross-platform/macios/unified/index.md)
- [Theading 基础知识](~/ios/app-fundamentals/threading.md)
- [委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [有关 `newrefcount`](~/ios/internals/newrefcount.md)

