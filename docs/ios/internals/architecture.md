---
title: iOS 应用程序体系结构
description: 本文档介绍 Xamarin.iOS 在较低级别，讨论如何本机和托管代码进行交互，AOT 编译、 选择器、 注册机构、 应用程序启动时，和生成器。
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 426c5ef5cc32877546ebb88cb485a81723816e6e
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675069"
---
# <a name="ios-app-architecture"></a>iOS 应用程序体系结构

Xamarin.iOS 应用程序运行在 Mono 的执行环境，并使用完整继续操作的时间 (AOT) 编译编译C#为 ARM 汇编语言代码。 这将在运行的同时使用[Objective C 运行时](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 这两个运行时环境上类似于 UNIX 的内核，特别是运行[XNU](https://en.wikipedia.org/wiki/XNU)，并公开到用户代码，从而允许开发人员访问基础本机或托管系统的各种 Api。

下图显示了此体系结构的基本概述：

[![](architecture-images/ios-arch-small.png "下图显示了继续操作的时间 (AOT) 编译体系结构的基本概述")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>本机代码和托管代码：说明

为 Xamarin 开发时条款*本机和托管*通常使用代码。 [托管代码](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是具有由其执行的代码[.NET Framework 公共语言运行时](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)，或在 Xamarin 的情况下： Mono 运行时。 这是我们所说的一种中间语言。

本机代码是将在特定平台 （例如，OBJECTIVE-C 或甚至 AOT 编译代码，ARM 芯片上的） 本机运行的代码。 本指南探讨了如何 AOT 编译托管的代码到本机代码，并说明中的 Xamarin.iOS 应用程序工作原理，如何充分利用通过绑定，使用 Apple 的 iOS Api 也具有访问权限。NET 的 BCL 和复杂的语言，如C#。

## <a name="aot"></a>AOT

当编译任何 Xamarin 平台应用程序，Mono C# (或F#) 编译器的运行时间以及将编译在C#和F#到 Microsoft 中间语言 (MSIL) 代码。 如果你在模拟器上运行 Xamarin.Android、 Xamarin.Mac 应用程序或甚至 Xamarin.iOS 应用程序[.NET 公共语言运行时 (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)编译实时 (JIT) 编译器中使用实时的 MSIL。 在此编译为本机代码的运行时，这可以在你的应用程序的正确体系结构上运行。

但是，是代码的在 iOS 上，设置由 Apple，将不允许在设备上动态生成执行安全限制。
若要确保我们遵守这些安全协议，Xamarin.iOS 改为使用继续操作的时间 (AOT) 编译器来编译托管的代码。 这将产生的本机 iOS 二进制，根据需要使用 LLVM 优化的设备，可以部署到 Apple 的基于 ARM 的处理器。 如何这相互配合的粗略关系图如下所示：

[![](architecture-images/aot.png "如何这相互配合的粗略的关系图")](architecture-images/aot-large.png#lightbox)

使用 AOT 具有许多限制，详见[限制](~/ios/internals/limitations.md)指南。 它还提供了许多改进通过 JIT 通过减少启动时间和各种性能优化

现在，我们已经浏览了代码如何从源编译为本机代码，让我们看实质上以查看如何 Xamarin.iOS 使我们能够编写完全本机的 iOS 应用程序

## <a name="selectors"></a>选择器

借助 Xamarin，我们有两个单独生态系统，.NET 和 Apple，我们需要将一起就显得越好，以确保最终目标是流畅的用户体验，简化。 我们已看到上面两个运行时通信的方式的部分中，您可能很好地听说过允许本机 iOS Api，用于在 Xamarin 中使用的术语绑定。 绑定中的深度中介绍了我们[OBJECTIVE-C 绑定](~/cross-platform/macios/binding/overview.md)文档，因此现在我们来探讨一下实质上的 iOS 工作原理。

首先，还有一种方法来公开到 Objective C C#，这是通过选择器。 一个选择器是一条消息发送到的对象或类。 Objective C 使用这是通过[objc_msgSend](~/cross-platform/macios/binding/overview.md)函数。
有关使用选择器的详细信息，请参阅[OBJECTIVE-C 选择器](~/ios/internals/objective-c-selectors.md)指南。 此外必须有一种方法来公开托管的代码到 OBJECTIVE-C 的这是更复杂，因为这样一个事实： Objective C 完全不了解托管代码。 若要解决此问题，我们使用*注册机构*。 下一节中更详细地介绍这些。

## <a name="registrars"></a>注册机构

如上所述，在注册机构是代码公开托管的代码到 Objective-c。 这是通过创建从 NSObject 派生每个托管类的列表：

- 对于不包装现有的 OBJECTIVE-C 类的所有类，它创建一个新的 Objective C 类具有镜像具有的所有托管的成员的 Objective C 成员 [`Export`] 属性。

- 在每个目标 – C 成员的实现中，代码将自动添加到调用托管的镜像的成员。

下面的伪代码演示如何完成此操作的示例：

**C#（托管的代码）**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objective c:**

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

托管的代码可以包含属性，`[Register]`和`[Export]`，注册机构用来知道该对象需要公开到 Objective-c。
`[Register]`特性用于指定生成的 Objective C 类的名称，以防默认生成的名称不适合。 从 NSObject 派生的所有类都自动都注册到 Objective-c。
所需`[Export]`属性包含一个字符串，这是在生成的 Objective C 类中使用的选择器。

有两种类型的注册机构中 Xamarin.iOS – 动态和静态使用：


- **动态注册机构**– 动态注册员尽在您的程序集在运行时中的所有类型的注册。 这是通过使用提供的函数[Objective C 的运行时 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 动态注册机构因此有速度较慢启动，但速度更快生成时间。 这是默认值为 iOS 模拟器。 使用动态注册机构时，调用 trampolines 的本机函数 （通常在 C 中)，使用作为方法实现。 它们不同的体系结构之间有所不同。

- **静态注册机构**– 静态注册机构会 Objective C 代码生成期间生成，然后到静态库编译并链接到可执行文件。 这使得更快的启动，但在生成时花费的时间。 默认情况下，这使用为设备版本。 静态注册机构还可与 iOS 模拟器通过传递`--registrar:static`作为`mtouch`属性在你的项目的生成选项中，如下所示：

    [![](architecture-images/image1.png "设置其他 mtouch 参数")](architecture-images/image1.png#lightbox)

有关具体细节 iOS 使用 Xamarin.iOS 类型注册系统的详细信息，请参阅[类型注册机构](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>应用程序启动

所有 Xamarin.iOS 可执行文件的入口点一个名为函数提供`xamarin_main`，其中初始化 mono。

具体取决于项目类型，以下是完成的：

- 有关常规 iOS 和 tvOS 应用程序，调用托管的 Main 方法中，提供的 Xamarin 应用程序。 此管理 Main 方法然后调用`UIApplication.Main`，这是 OBJECTIVE-C 的入口点 UIApplication.Main 是适用于 OBJECTIVE-C 的绑定`UIApplicationMain`方法。
- 为了扩展和本机函数 – `NSExtensionMain`或 (`NSExtensionmain` WatchOS 扩展) – 提供由 Apple 调用库。 由于这些项目是类库和非可执行项目，则没有托管的 Main 方法来执行。

此启动序列的所有编译到一个静态库，以便您的应用程序知道如何在开始然后链接到最终可执行文件。

此时我们的应用程序已启动、 运行 Mono、 我们处于托管代码和我们知道如何调用本机代码和回叫。 我们需要做的下一件事是实际开始添加控件并使应用程序交互。


## <a name="generator"></a>Generator

Xamarin.iOS 包含每个单个 iOS API 的定义。 可以在浏览其中的任何[MaciOS github 存储库](https://github.com/xamarin/xamarin-macios/tree/master/src)。 这些定义包含接口的属性，以及任何必需的方法和属性。 例如，下面的代码用于定义在 UIKit 中 UIToolbar[命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)。 请注意它是具有多种方法和属性的接口：

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

名为的生成器[ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs)在 Xamarin.iOS 中，将这些定义文件，并使用.NET 工具[其编译到一个临时程序集](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 但是，此临时程序集不易于使用，以调用 Objective C 代码。 该生成器，然后读取的临时程序集并生成C#可以在运行时使用的代码。
这是为什么，例如，如果将随机属性添加到你定义的.cs 文件，它不会显示在输出的代码中。 生成器不知道它，因此`btouch`尚不知道要在临时程序集其输出中查找它。

一旦创建 Xamarin.iOS.dll 后，mtouch 将绑定的所有组件在一起。

在高级别，来实现这通过执行以下任务：

-   创建应用捆绑包结构。
-   将复制你的托管程序集中。
-   如果启用了链接，运行托管链接器以优化您的程序集的翻录出未使用的部分。
-   AOT 编译。
-   创建本机可执行文件，输出静态库的链接到本机可执行文件 （一个用于每个程序集） 的一系列，因此本机可执行文件组成的启动器代码、 注册机构代码 （如果静态） 和 AOT 中的所有输出编译器


有关详细信息链接器和使用方式的详细信息，请参阅[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南介绍了 Xamarin.iOS 应用和浏览了的 Xamarin.iOS 和它与 OBJECTIVE-C 的深度中的关系的 AOT 编译。

## <a name="related-links"></a>相关链接

- [限制](~/ios/internals/limitations.md)
- [绑定 Objective-C](~/cross-platform/macios/binding/overview.md)
- [Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)
- [类型注册机构](~/ios/internals/registrar.md)
- [链接器](~/ios/deploy-test/linker.md)
