---
title: Xamarin.Mac 体系结构
description: 本指南介绍了到较低级别的 Objective C 的 Xamarin.Mac 和它的关系。 它说明了概念，如编译、 选择器、 注册机构、 应用程序启动和生成器。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 1ea38b527acaa89b9f25690de4e55664a7afd9e8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034117"
---
# <a name="xamarinmac-architecture"></a>Xamarin.Mac 体系结构

_本指南介绍了到较低级别的 Objective C 的 Xamarin.Mac 和它的关系。它说明了概念，如编译、 选择器、 注册机构、 应用程序启动和生成器。_

## <a name="overview"></a>概述

Xamarin.Mac 应用程序运行在 Mono 的执行环境，并使用 Xamarin 的编译器来编译到中间语言 (IL)，即然后中实时 (JIT) 编译为本机代码在运行时。 这将在运行的同时与 Objective C 运行时。 这两个运行时环境类似于 UNIX 的内核，特别是 XNU，顶层运行，并公开到用户代码，从而允许开发人员访问基础本机或托管系统的各种 Api。

下图显示了此体系结构的基本概述：

[![显示的体系结构的基本概述关系图](architecture-images/mac-arch.png "显示体系结构的基本概述关系图")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>本机和托管代码

为 Xamarin 开发时条款*本机*并*托管*通常使用代码。 托管的代码是已由.NET Framework 公共语言运行时，或在 Xamarin 的情况下管理其执行的代码： Mono 运行时。

本机代码是将在特定平台 （例如，OBJECTIVE-C 或甚至 AOT 编译代码，ARM 芯片上的） 本机运行的代码。 本指南介绍如何在托管的代码编译为本机代码，并说明如何 Xamarin.Mac 应用程序的工作原理，充分利用通过绑定，使用 Apple Mac Api 还同时访问。NET 的 BCL 和复杂的语言，如C#。

## <a name="requirements"></a>要求

以下是通过 Xamarin.Mac 开发 macOS 应用程序所需的条件：

- Mac 运行 macOS Sierra (10.12) 或更高版本。
- Xcode 的最新版本 (安装有[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- 最新版本的 Xamarin.Mac 和 Visual Studio for Mac

运行通过 Xamarin.Mac 创建的 Mac 应用程序具有以下系统要求：

- 运行 Mac OS X 10.7 或更高版本的 Mac。

## <a name="compilation"></a>编译

当编译任何 Xamarin 平台应用程序，Mono C# (或F#) 编译器的运行时间以及将编译在C#和F#为 Microsoft 中间语言 （MSIL 或 IL） 代码。 然后使用 Xamarin.Mac*只需在实时 (JIT)* 编译器在运行时编译本机代码中，根据需要允许在正确的体系结构上执行。

这与使用 AOT 编译的 Xamarin.iOS。 使用 AOT 编译器时，在生成时被编译所有程序集以及其中的所有方法。 Jit，编译发生在仅对执行的方法的需求上。

Xamarin.Mac 应用程序，Mono 通常嵌入到应用程序捆绑包 (称为**嵌入 Mono**)。 使用经典的 Xamarin.Mac API 时，可以改为使用应用程序**系统 Mono**，但是，这不受支持的统一 API 中。 系统 Mono 是指在操作系统中已安装的 Mono。 应用程序启动时，Xamarin.Mac 应用将使用此。

## <a name="selectors"></a>选择器

借助 Xamarin，我们有两个单独生态系统，.NET 和 Apple，我们需要将一起就显得越好，以确保最终目标是流畅的用户体验，简化。 我们已看到上面两个运行时通信的方式的部分中，您可能很好地听说过允许本机 Mac Api，若要在 Xamarin 中使用的术语绑定。 在中深入介绍了绑定[OBJECTIVE-C 绑定文档](~/mac/platform/binding.md)，因此现在，我们来探讨一下 Xamarin.Mac 实质上的工作方式。

首先，还有一种方法来公开到 Objective C C#，这是通过选择器。 一个选择器是一条消息发送到的对象或类。 Objective C 使用这是通过[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函数。 有关使用选择器的详细信息，请参阅 iOS [OBJECTIVE-C 选择器](~/ios/internals/objective-c-selectors.md)指南。 此外必须有一种方法来公开托管的代码到 OBJECTIVE-C 的这是更复杂，因为这样一个事实： Objective C 完全不了解托管代码。 若要解决此问题，我们使用[注册机构](~/mac/internals/registrar.md)。 这在下一节中详细说明。

## <a name="registrar"></a>注册器

如上所述，在注册机构是代码公开托管的代码到 Objective-c。 这是通过创建从 NSObject 派生每个托管类的列表：

- 对于不包装现有的 OBJECTIVE-C 类的所有类，它创建一个新的 Objective C 类具有镜像具有的所有托管的成员的 Objective C 成员`[Export]`属性。
- 在每个目标 – C 成员的实现中，代码将自动添加到调用托管的镜像的成员。

下面的伪代码演示如何完成此操作的示例：

**C#（托管的代码）：**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective C （本机代码）：**

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

托管的代码可以包含属性，`[Register]`和`[Export]`，注册机构用来知道该对象需要公开到 Objective-c。 [注册] 属性用于在生成的默认名称不适合的情况下指定生成的 Objective C 类的名称。 从 NSObject 派生的所有类都自动都注册到 Objective-c。 所需的 [导出] 属性包含一个字符串，这是在生成的 Objective C 类中使用的选择器。

有两种类型的注册机构中 Xamarin.Mac – 动态和静态使用：

- 动态注册机构 – 这是所有 Xamarin.Mac 生成的默认注册机构。 动态注册机构执行的程序集在运行时中的所有类型的注册。 通过使用提供的 Objective C 的运行时 API 函数执行此操作。 动态注册机构因此有速度较慢启动，但速度更快生成时间。 使用动态注册机构时，调用 trampolines 的本机函数 （通常在 C 中)，使用作为方法实现。 它们不同的体系结构之间有所不同。
- 静态注册机构 – 静态注册机构期间生成，然后到静态库编译并链接到可执行文件生成 Objective C 代码。 这使得更快的启动，但在生成时花费的时间。

## <a name="application-launch"></a>应用程序启动

Xamarin.Mac 的启动逻辑将有所不同，具体取决是否嵌入，或使用系统 Mono。 若要查看的代码和 Xamarin.Mac 应用程序启动的步骤，请参阅[启动标头](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)xamarin macios 公共存储库中的文件。

## <a name="generator"></a>Generator

Xamarin.Mac 包含每个 Mac API 的定义。 可以在浏览其中的任何[MaciOS github 存储库](https://github.com/xamarin/xamarin-macios/tree/master/src)。 这些定义包含接口的属性，以及任何必需的方法和属性。 例如，使用以下代码来定义在 NSBox [AppKit 命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)。 请注意它是具有多种方法和属性的接口：

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

生成器，名为`bmac`在 Xamarin.Mac，采用这些定义文件，并使用.NET 工具将其编译到一个临时程序集。 但是，此临时程序集不易于使用，以调用 Objective C 代码。 该生成器，然后读取的临时程序集并生成C#可以在运行时使用的代码。 这是为什么，例如，如果将随机属性添加到你定义的.cs 文件，它不会显示在输出的代码中。 生成器不知道它，并因此`bmac`尚不知道要在临时程序集其输出中查找它。

Xamarin.Mac.dll 创建后，使用包装程序， `mmp`，将捆绑的所有组件在一起。

在高级别，来实现这通过执行以下任务：

- 创建应用捆绑包结构。
- 将复制你的托管程序集中。
- 如果启用了链接，运行托管的链接器优化你的程序集通过删除未使用的部分。
- 创建启动器应用程序，以及如果是在静态模式下的注册机构代码谈到的启动程序代码中的链接。

这是以用户的一部分运行生成过程，从而将用户代码编译为程序集，该引用 Xamarin.Mac.dll 和运行则`mmp`以使其包

有关详细信息链接器和使用方式的详细信息，请参阅 iOS[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南介绍了编译的 Xamarin.Mac 应用，并浏览了的 Xamarin.Mac 和及其与 Objective-c。
