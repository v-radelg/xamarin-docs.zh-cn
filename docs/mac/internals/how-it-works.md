---
title: Xamarin.Mac 的工作原理
description: 本文档介绍了 Xamarin 的内部工作原理。 具体而言，它会查看构造函数、内存管理、预先编译和注册器。
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/25/2017
ms.openlocfilehash: 24ddd71fe1468edc70ec4d487dc2cb2dbd4da1b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769813"
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的工作原理

但在大多数情况下，开发人员始终都无需担心 Xamarin 的内部 "神奇" 的问题，但是，大致了解功能在幕后的工作方式将有助于使用C#镜头和调试来解释现有文档出现问题。

在 Xamarin 中，应用程序可桥接两个领域：基于目标 C 的运行时包含本机类的实例（`NSString`、 `NSApplication`等），并且C#运行时包含托管类的实例（`System.String`、 `HttpClient`等）。 在这两个领域之间，Xamarin 会创建一个双向桥，使应用可以调用目标 c （如`NSApplication.Init`）中的方法（选择器），而客观-c 可以调用应用的C#方法（如应用委托上的方法）。 通常情况下，通过**P/invoke**和某些运行时代码 Xamarin 提供对目标 C 的调用。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>向C#目标公开类/方法-C

但是，若要返回到应用的C#对象，则需要以目标 c 可以理解的方式公开。 这是通过和`Register` `Export`属性来完成的。 请参见以下示例：

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

在此示例中，对于名为`MyClass` `init`和`run`的选择器，目标 C 运行时现在将了解名为的类。

在大多数情况下，这是一个实现细节，开发人员`base`可忽略此类实现，因为应用接收的大多数回调都是通过类（ `AppDelegate`如、 `Delegates`、 `DataSources`）或**操作**上的重写方法来实现的。传入 Api。 在所有这些情况下， `Export` C#代码中都不需要属性。

## <a name="constructor-runthrough"></a>构造函数浏览

在许多情况下，开发人员需要将应用程序的C#类构造 API 公开给目标-C 运行时，以便可以从诸如在情节提要或 XIB 文件中调用时这样的位置对其进行实例化。 下面是 Xamarin 应用程序中使用的五个最常见的构造函数：

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

通常，开发人员应保留在创建`IntPtr`某些`NSCoder`类型（例如自定义`NSViews` ）时生成的和构造函数。 如果 Xamarin 需要调用这些构造函数之一来响应目标 C 运行时请求，并且删除了该程序，则该应用程序将在本机代码中崩溃，可能很难准确确定问题所在。

## <a name="memory-management-and-cycles"></a>内存管理和周期

Xamarin 中的内存管理的方式非常类似于 Xamarin。 它也是一个复杂的主题，它超出了本文档的范围。 请阅读[内存和性能最佳实践](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>提前编译

通常情况下，.NET 应用程序在生成时不会向下编译到计算机代码中，而是编译成一个名为 IL 代码的中间层，以在启动应用程序时，将_实时_（JIT）编译为机器代码。

Mono 运行时 JIT 编译此计算机代码所需的时间可能会使 Xamarin 应用程序的启动速度降低到 20%，因为需要生成必要的计算机代码。

由于 Apple 在 iOS 上施加了限制，因此不能对 IL 使用 JIT 编译。 因此，所有 Xamarin iOS 应用都是在生成周期内编译为计算机_代码的全职（AOT_ ）。

Xamarin 的新功能是在应用程序生成周期内对 IL 代码进行 AOT 的功能，就像 Xamarin 一样。 Xamarin 使用_混合_AOT 方法，该方法编译大多数所需的机器代码，但允许运行时编译所需的 trampolines，并灵活地继续支持反射。发出（以及当前正在处理的其他用例Xamarin）。

AOT 可以通过两个主要方面来帮助 Xamarin 应用：

- **更好的 "本机" 崩溃日志**-如果 Xamarin Mac 应用程序在本机代码中崩溃，这在对 Cocoa api （例如将发送`null`到不接受它的方法）的本机崩溃日志中进行无效调用时常见难以分析。 由于 JIT 帧不包含调试信息，因此将有多行包含十六进制偏移，而不会有任何线索发生。 AOT 生成 "真实" 的命名帧，跟踪的可读性更强。 这也意味着 Xamarin 应用程序将更好地与**lldb**和**乐器**等本机工具进行交互。
- **更好的启动时间性能**-对于大型 Xamarin Mac 应用程序，如果有多秒启动时间，JIT 编译所有代码可能需要很长时间。 AOT 会提前完成此操作。

### <a name="enabling-aot-compilation"></a>启用 AOT 编译

通过双击 "**解决方案资源管理器**中的**项目名称**，导航到" **Mac 生成**"并将其添加`--aot:[options]`到"**其他 mmp 参数：** "字段（其中`[options]`是一个或多个用于控制 AOT 类型的选项，请参阅下文）。 例如:

![将 AOT 添加到其他 mmp 参数](how-it-works-images/aot01.png "将 AOT 添加到其他 mmp 参数")

> [!IMPORTANT]
> 启用 AOT 编译会大幅增加生成时间（有时最多几分钟），但它可以将应用启动时间平均提高 20%。 因此，只应在 Xamarin 应用程序的**发布**版本中启用 AOT 编译。

### <a name="aot-compilation-options"></a>Aot 编译选项

在 Xamarin 应用程序上启用 AOT 编译时，有几种不同的选项可以进行调整：

- `none`-无 AOT 编译。 此为默认设置。
- `all`-AOT 编译 MonoBundle 中的每个程序集。
- `core`-AOT 编译`Xamarin.Mac` `System`和`mscorlib`程序集。
- `sdk`-AOT 编译`Xamarin.Mac`和基类库（BCL）程序集。
- `|hybrid`-将此项添加到上述选项之一后，将启用混合 AOT，这允许进行 IL 剥离，但会导致编译时间较长。
- `+`-包含用于 AOT 编译的单个文件。
- `-`-从 AOT 编译中删除单个文件。

例如`--aot:all,-MyAssembly.dll` ，将在 MonoBundle 中的所有程序集上启用 AOT 编译，_但_ `MyAssembly.dll` `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`会`mscorlib.dll`启用混合，代码 AOT 包含`MyOtherAssembly.dll`和排除。

## <a name="partial-static-registrar"></a>部分静态注册器

在开发 Xamarin 应用程序时，最大程度地减少完成更改与测试之间的时间可能对满足开发截止时间很重要。 基本代码和单元测试的模块化等策略有助于缩短编译时间，因为这会减少应用需要昂贵的完全重建的次数。

此外，和新的 Xamarin Mac、_部分静态注册_器（如 Xamarin）可以显著减少**调试**配置中 Xamarin 应用程序的启动时间。 了解在调试启动中使用部分静态注册器会对几乎5倍的改善进行的影响，将需要一些背景知识来决定注册器的定义、静态和动态之间的差异，以及这种 "部分静态" 版本的作用。

### <a name="about-the-registrar"></a>关于注册机构

任何 Xamarin 应用程序的本质都是 Apple 和目标-C 运行时中的 Cocoa 框架。 在这种C# "本机世界" 和 "托管世界" 之间建立桥梁是 Xamarin 的主要责任。 此任务的一部分由注册机构处理，在方法内`NSApplication.Init ()`执行。 这是在 Xamarin 中使用 Cocoa api 的一个原因是需要`NSApplication.Init`先调用。

注册C#器的工作是将从类派生的应用类（ `NSApplicationDelegate`如、 `NSView` `NSWindow`、和`NSObject`）的存在情况通知给目标 C 运行时。 这需要扫描应用中的所有类型，以确定需要注册的内容以及每种类型上要报告的元素。

此扫描可以在启动应用程序时使用反射**动态**完成，也可以**静态**方式完成，作为生成时步骤。 选择注册类型时，开发人员应注意以下事项：

- 静态注册可显著减少启动时间，但可能会显著降低生成时间（通常比双重调试生成时间多）。 这将是**发布**配置生成的默认值。
- 动态注册会在应用程序启动和跳过代码生成之前延迟此操作，但此附加工作可在应用程序启动中创建明显的暂停（至少两秒钟）。 这在调试配置生成（默认为动态注册，其反射速度较慢）中尤其明显。

在 Xamarin 8.13 中首次引入的部分静态注册为开发人员提供了这两个选项的最佳选择。 通过在`Xamarin.Mac.dll`静态库中预计算每个元素的注册信息并将此信息与 Xamarin 一起发送（仅需要在生成时链接到），Microsoft 已删除动态注册器，但不影响生成时间。

### <a name="enabling-the-partial-static-registrar"></a>启用部分静态注册器

通过双击 "**解决方案资源管理器**中的**项目名称**，导航到" **Mac 生成**"并将其添加`--registrar:static`到"**其他 mmp 参数：** "字段，在 Xamarin 中启用部分静态注册器。 例如:

![向其他 mmp 参数添加部分静态注册]器(how-it-works-images/psr01.png "向其他 mmp 参数添加部分静态注册")器

## <a name="additional-resources"></a>其他资源

下面是有关如何在内部工作的详细说明：

- [Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)
- [注册器](~/ios/internals/registrar.md)
- [适用于 iOS 和 OS X 的 Xamarin Unified API](~/cross-platform/macios/unified/index.md)
- [Theading 基础知识](~/ios/app-fundamentals/threading.md)
- [委托、协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [有关`newrefcount`](~/ios/internals/newrefcount.md)
