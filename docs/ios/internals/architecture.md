---
title: iOS 应用程序体系结构
description: 本文档以较低的级别描述 Xamarin，并讨论本机和托管代码如何交互、AOT 编译、选择器、注册器、应用程序启动和生成器。
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e5dbc04e52aea4307716c343df5757d0fe012b74
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022391"
---
# <a name="ios-app-architecture"></a>iOS 应用程序体系结构

Xamarin iOS 应用程序在 Mono 执行环境中运行，并使用完全提前（AOT）编译将代码编译C#为 ARM 汇编语言。 这与[目标 C 运行时](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)并行运行。 这两个运行时环境都在类似于 UNIX 的内核（特别是[XNU](https://en.wikipedia.org/wiki/XNU)）的基础上运行，并向用户代码公开各种 api，使开发人员能够访问基础本机或托管系统。

下图显示了此体系结构的基本概述：

[![](architecture-images/ios-arch-small.png "This diagram shows a basic overview of the Ahead of Time (AOT) compilation architecture")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>本机代码和托管代码：说明

当开发 Xamarin 时，通常使用术语 "*本机" 和 "托管*代码"。 [托管代码](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是由[.NET Framework 公共语言运行时](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)或 Xamarin 的情况下，由其执行的代码： Mono 运行时。 这就是我们称之为中间语言的。

本机代码是在特定平台（例如，目标平台，甚至是在 ARM 芯片上的 AOT 编译代码）上运行的代码。 本指南探讨了 AOT 如何将托管代码编译为本机代码，并说明了 Xamarin iOS 应用程序的工作原理，充分利用了 Apple 的 iOS Api 的使用绑定，同时还具有对的访问权限。网络的 BCL 和复杂的语言（如C#）。

## <a name="aot"></a>AOT

编译任何C# Xamarin 平台应用程序时，Mono （或F#）编译器将运行，并将C#和F#代码编译为 Microsoft 中间语言（MSIL）。 如果运行的是 xamarin、Xamarin 应用程序，甚至是模拟器上的 Xamarin iOS 应用程序，则[.Net 公共语言运行时（CLR）](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)使用实时（JIT）编译器编译 MSIL。 在运行时，这会被编译为本机代码，该代码可在应用程序的正确体系结构上运行。

但是，iOS 上存在安全限制，由 Apple 设置，这不允许在设备上执行动态生成的代码。
为了确保我们遵守这些安全协议，Xamarin 改为使用提前（AOT）编译器来编译托管代码。 这将生成本机 iOS 二进制文件，还可以选择使用 LLVM for 设备进行优化，这些二进制文件可以部署在 Apple 基于 ARM 的处理器上。 下面说明了如何将这一组合在一起的大致示意图：

[![](architecture-images/aot.png "A rough diagram of how this fits together")](architecture-images/aot-large.png#lightbox)

使用 AOT 有多种限制，这在[限制](~/ios/internals/limitations.md)指南中进行了详细介绍。 它还通过缩短启动时间和各种性能优化，对 JIT 进行了大量改进。

现在，我们已经探讨了如何将代码从源代码编译为本机代码，接下来让我们看看如何使用 Xamarin 来编写完全本机 iOS 应用程序

## <a name="selectors"></a>选择器

使用 Xamarin，我们有两个独立的生态系统（.NET 和 Apple），我们需要将它们整合在一起，以确保最终目标是一个流畅的用户体验。 在上面的部分中，我们看到了两个运行时的通信方式，你可能听说过术语 "绑定"，它允许在 Xamarin 中使用本机 iOS Api。 我们的[目标-C 绑定](~/cross-platform/macios/binding/overview.md)文档深入介绍了绑定，因此，现在让我们来了解 iOS 如何工作。

首先，必须有一种方法将目标-C 公开给C#，这是通过选择器完成的。 选择器是发送到对象或类的消息。 对于目标-C，这是通过[objc_msgSend](~/cross-platform/macios/binding/overview.md)函数实现的。
有关使用选择器的详细信息，请参阅[目标-C 选择器](~/ios/internals/objective-c-selectors.md)指南。 还必须有一种方法将托管代码公开给目标-C，这更复杂，因为目标-C 不知道托管代码的任何内容。 为此，我们使用*注册机构*。 下一节将对这些内容进行更详细的介绍。

## <a name="registrars"></a>域名

如上所述，注册机构是向目标-C 公开托管代码的代码。 它通过创建从 NSObject 派生的每个托管类的列表来实现此操作：

- 对于未包装现有目标-C 类的所有类，它将创建一个新的目标-c 类，其中目标为 c 的成员镜像具有 [`Export`] 特性的所有托管成员。

- 在每个目标– C 成员的实现中，会自动添加代码以调用镜像托管成员。

下面的伪代码演示了如何执行此操作的示例：

**C#（托管代码）**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**目标-C：**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

托管代码可以包含属性，`[Register]` 和 `[Export]`，注册机构使用该属性来了解需要向目标-C 公开对象。
如果默认生成的名称不合适，则使用 `[Register]` 特性来指定生成的目标 C 类的名称。 派生自 NSObject 的所有类都将自动注册到目标 C。
必需的 `[Export]` 属性包含一个字符串，该字符串是在生成的目标 C 类中使用的选择器。

在 Xamarin 中使用了两种类型的注册机构–动态和静态：

- **动态注册机构**–动态注册机构会在运行时注册程序集中的所有类型。 它通过使用由[目标 C 的运行时 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)提供的函数来实现此目的。 因此，动态注册器的启动速度较慢，但生成时间更快。 这是 iOS 模拟器的默认设置。 使用动态注册机构时，本机函数（通常为 C）（称为 trampolines）用作方法实现。 它们在不同的体系结构之间有所不同。

- **静态注册机构**–静态注册机构在生成过程中生成目标 C 代码，然后将其编译为静态库并链接到可执行文件。 这样就可以更快地启动，但在生成过程中花费的时间更长。 默认情况下，此设置用于设备生成。 静态注册器还可与 iOS 模拟器结合使用，方法是将 `--registrar:static` 作为 `mtouch` 特性传递到项目的生成选项中，如下所示：

    [![](architecture-images/image1.png "Setting Additional mtouch arguments")](architecture-images/image1.png#lightbox)

有关 Xamarin 使用的 iOS 类型注册系统的详细信息，请参阅[类型注册](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>应用程序启动

所有 Xamarin iOS 可执行文件的入口点由称为 `xamarin_main`的函数提供，该函数可初始化 mono。

根据项目类型，将执行以下操作：

- 对于常规的 iOS 和 tvOS 应用程序，将调用 Xamarin 应用提供的托管 Main 方法。 然后，此托管 Main 方法会调用 `UIApplication.Main`，这是目标 C 的入口点。 UIApplication 是目标 `UIApplicationMain` 方法的绑定。
- 对于扩展，将调用 Apple 库提供的本机函数- `NSExtensionMain` 或（WatchOS 扩展的`NSExtensionmain`）。 由于这些项目是类库而不是可执行项目，因此不需要执行托管的主要方法。

所有此启动序列均编译为静态库，然后将其链接到最终的可执行文件，以便您的应用程序了解如何实现基础。

此时，我们的应用程序已启动，Mono 正在运行，我们在托管代码中，我们知道如何调用本机代码并回调。 接下来我们要做的是实际开始添加控件，并使应用程序成为交互的。

## <a name="generator"></a>Generator

Xamarin 包含每个 iOS API 的定义。 可在[MaciOS github](https://github.com/xamarin/xamarin-macios/tree/master/src)存储库上浏览任何这些。 这些定义包含具有属性的接口，以及任何所需的方法和属性。 例如，下面的代码用于定义 UIKit[命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)中的 UIToolbar。 请注意，它是一个具有多个方法和属性的接口：

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

在 Xamarin 中称为[`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs)的生成器使用这些定义文件，并使用 .net 工具将[其编译为临时程序集](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 但是，此临时程序集不能用于调用目标 C 代码。 然后，生成器将读取临时程序集， C#并生成可在运行时使用的代码。
例如，如果将随机属性添加到 .cs 文件中，则该属性不会显示在输出的代码中。 生成器并不知道它，因此 `btouch` 不知道要将其输出到临时程序集中。

创建 Xamarin .dll 后，mtouch 会将所有组件捆绑在一起。

从较高层次来看，通过执行以下任务实现此操作：

- 创建应用捆绑包结构。
- 复制托管程序集中的。
- 如果启用了链接，请运行托管链接器，以通过翻录未使用的部分来优化程序集。
- AOT 编译。
- 创建一个本机可执行文件，用于输出链接到本机可执行文件中的一系列静态库（每个程序集都有一个），以使本机可执行文件包含启动器代码、注册器代码（如果为静态）和来自 AOT 的所有输出编译程序

有关链接器及其使用方式的详细信息，请参阅[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南探讨了 Xamarin iOS 应用的 AOT 编译，并深入探讨了与目标-C 之间的关系。

## <a name="related-links"></a>相关链接

- [限制](~/ios/internals/limitations.md)
- [绑定 Objective-C](~/cross-platform/macios/binding/overview.md)
- [目标-C 选择器](~/ios/internals/objective-c-selectors.md)
- [类型注册机构](~/ios/internals/registrar.md)
- [链接器](~/ios/deploy-test/linker.md)
