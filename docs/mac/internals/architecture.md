---
title: Xamarin 体系结构
description: 本指南将在较低级别探索 Xamarin 和与其在目标-C 之间的关系。 它介绍了一些概念, 如编译、选择器、注册机构、应用程序启动和生成器。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 61a5757c20f3a39df583bda10a11145e04560bf8
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198205"
---
# <a name="xamarinmac-architecture"></a>Xamarin 体系结构

_本指南将在较低级别探索 Xamarin 和与其在目标-C 之间的关系。它介绍了一些概念, 如编译、选择器、注册机构、应用程序启动和生成器。_

## <a name="overview"></a>概述

Xamarin 应用程序在 Mono 执行环境中运行, 并使用 Xamarin 的编译器向下编译到中间语言 (IL), 然后在运行时实时 (JIT) 编译为本机代码。 这与目标 C 运行时并行运行。 这两个运行时环境都在类似于 UNIX 的内核 (特别是 XNU) 的基础上运行, 并向用户代码公开各种 Api, 使开发人员能够访问基础本机或托管系统。

下图显示了此体系结构的基本概述:

[![显示体系结构基本概述的关系图](architecture-images/mac-arch.png "显示体系结构基本概述的关系图")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>本机代码和托管代码

为 Xamarin 进行开发时, 通常使用术语 "*本机*" 和 "*托管*代码"。 托管代码是由 .NET Framework 公共语言运行时或 Xamarin 的情况下, 由其执行的代码: Mono 运行时。

本机代码是在特定平台 (例如, 目标平台, 甚至是在 ARM 芯片上的 AOT 编译代码) 上运行的代码。 本指南探讨了如何将托管代码编译为本机代码, 并说明了 Xamarin 应用程序的工作原理, 通过使用绑定, 充分利用了 Apple 的 Mac Api, 同时还具有对的访问权限。网络的 BCL 和复杂的语言 (如C#)。

## <a name="requirements"></a>要求

以下是通过 Xamarin.Mac 开发 macOS 应用程序所需的条件：

- 运行 macOS Sierra (10.12) 或更高版本的 Mac。
- 最新版本的 Xcode (从[应用商店](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)安装)
- 最新版本的 Xamarin 和 Visual Studio for Mac

运行通过 Xamarin.Mac 创建的 Mac 应用程序具有以下系统要求：

- 运行 Mac OS X 10.7 或更高版本的 Mac。

## <a name="compilation"></a>编译

编译任何C# Xamarin 平台应用程序时, Mono (或F#) 编译器将运行, 并将C#和F#代码编译为 Microsoft 中间语言 (MSIL 或 IL)。 然后, Xamarin 在运行时使用*实时 (JIT)* 编译器来编译本机代码, 从而允许根据需要在正确的体系结构上执行。

这与使用 AOT 编译的 Xamarin 不同。 使用 AOT 编译器时, 将在生成时编译所有程序集和其中的所有方法。 对于 JIT, 只需对执行的方法进行编译。

通过 Xamarin Mac 应用程序, Mono 通常嵌入到应用程序捆绑包 (称为**嵌入 Mono**)。 使用经典 Xamarin Mac API 时, 应用程序可以改用**系统 Mono**, 不过, 在 Unified API 中不支持这种情况。 系统 Mono 指的是在操作系统中安装的 Mono。 在应用程序启动时, Xamarin 应用将使用此。

## <a name="selectors"></a>选择器

使用 Xamarin, 我们有两个独立的生态系统 (.NET 和 Apple), 我们需要将它们整合在一起, 以确保最终目标是一个流畅的用户体验。 在上面的部分中, 我们看到了两个运行时的通信方式, 你可能听说过术语 "绑定", 它允许在 Xamarin 中使用本机 Mac Api。 在[目标-C 绑定文档](~/mac/platform/binding.md)中深入介绍了绑定, 因此, 我们现在来了解如何在幕后使用 Xamarin。

首先, 必须有一种方法将目标-C 公开给C#, 这是通过选择器完成的。 选择器是发送到对象或类的消息。 对于目标-C, 这是通过[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函数实现的。 有关使用选择器的详细信息, 请参阅 iOS[目标-C 选择器](~/ios/internals/objective-c-selectors.md)指南。 还必须有一种方法将托管代码公开给目标-C, 这更复杂, 因为目标-C 不知道托管代码的任何内容。 为此, 我们使用[注册机构](~/mac/internals/registrar.md)。 下一节将对此进行更详细的介绍。

## <a name="registrar"></a>注册器

如上所述, 注册机构是向目标-C 公开托管代码的代码。 它通过创建从 NSObject 派生的每个托管类的列表来实现此操作:

- 对于未包装现有目标-c 类的所有类, 它将创建一个新的目标-c 类, 其中目标为 c 的成员镜像具有`[Export]`属性的所有托管成员。
- 在每个目标– C 成员的实现中, 会自动添加代码以调用镜像托管成员。

下面的伪代码演示了如何执行此操作的示例:

**C#(托管代码):**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**目标-C (本机代码):**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

托管代码可包含特性`[Register]`和`[Export]`, 注册器使用这些特性来了解需要向目标-C 公开对象。 [Register] 特性用于指定生成的目标 C 类的名称, 以防默认生成的名称不合适。 派生自 NSObject 的所有类都将自动注册到目标 C。 必需的 [Export] 特性包含一个字符串, 该字符串是在生成的目标 C 类中使用的选择器。

Xamarin 中使用了两种类型的注册机构–动态和静态:

- 动态注册机构–这是所有 Xamarin build 的默认注册机构。 动态注册器会在运行时注册程序集中的所有类型。 它通过使用由目标 C 的运行时 API 提供的函数来实现此目的。 因此, 动态注册器的启动速度较慢, 但生成时间更快。 使用动态注册机构时, 本机函数 (通常为 C) (称为 trampolines) 用作方法实现。 它们在不同的体系结构之间有所不同。
- 静态注册机构–静态注册机构在生成过程中生成目标 C 代码, 然后将其编译为静态库并链接到可执行文件。 这样就可以更快地启动, 但在生成过程中花费的时间更长。

## <a name="application-launch"></a>应用程序启动

Xamarin 启动逻辑将因使用嵌入的还是系统 Mono 而有所不同。 若要查看 Xamarin 应用程序启动的代码和步骤, 请参阅 macios 公用存储库中的[启动标头](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)文件。

## <a name="generator"></a>Generator

Xamarin 包含每个 Mac API 的定义。 可在[MaciOS github](https://github.com/xamarin/xamarin-macios/tree/master/src)存储库上浏览任何这些。 这些定义包含具有属性的接口, 以及任何所需的方法和属性。 例如, 以下代码用于定义[AppKit 命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)中的 NSBox。 请注意, 它是一个具有多个方法和属性的接口:

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

在 Xamarin 中调用`bmac`的生成器使用这些定义文件, 并使用 .net 工具将其编译为临时程序集。 但是, 此临时程序集不能用于调用目标 C 代码。 然后, 生成器将读取临时程序集, C#并生成可在运行时使用的代码。 例如, 如果将随机属性添加到 .cs 文件中, 则该属性不会显示在输出的代码中。 生成器并不知道它, 因此`bmac` , 在临时程序集中找不到它的输出。

创建 Xamarin 后, 包装`mmp`器会将所有组件捆绑在一起。

从较高层次来看, 通过执行以下任务实现此操作:

- 创建应用捆绑包结构。
- 复制托管程序集中的。
- 如果启用了链接, 请运行托管链接器以通过删除未使用的部分来优化程序集。
- 创建启动器应用程序, 如果在静态模式下, 则在启动器代码中进行链接将与注册器代码一起讨论。

然后, 此操作将作为用户生成过程的一部分运行, 该过程将用户代码编译到一个程序集中, 该程序集用于`mmp`引用

有关链接器及其使用方式的详细信息, 请参阅 iOS[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南介绍了 Xamarin. Mac 应用程序的编译, 并探讨了如何将 Xamarin 与目标 C 进行了关联。
