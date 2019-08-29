---
title: 本机引用 iOS、Mac 和绑定项目
description: 本机引用使你能够将本机框架嵌入到 Xamarin、Xamarin 或绑定项目。
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e5e232ffa8a41f7adbffae595b85341a10d8667a
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065257"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>IOS、Mac 和绑定项目中的本机引用

_本机引用使你能够将本机框架嵌入到 Xamarin 或 Xamarin 或 Xamarin 项目或绑定项目。_

从 iOS 8.0 开始, 可以创建一个嵌入框架, 以便在 Xcode 中的应用扩展和主应用之间共享代码。 使用本机引用功能, 可以在 Xamarin 中使用这些嵌入式框架 (使用 Xcode 创建)。
 
> [!IMPORTANT]
> 不能从任何类型的 Xamarin 或 Xamarin 项目创建嵌入的框架, 本机引用仅允许使用现有的本机 (目标-C) 框架。

<a name="Terminology" />

## <a name="terminology"></a>术语

在 iOS 8 (及更高版本) 中,**嵌入的框架**既可以是嵌入的静态链接框架, 也可以是动态链接框架。 若要正确分发它们, 必须使其成为 "fat" 框架, 这些框架包含了你要在应用中支持的每个设备体系结构的所有_切片_。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>静态与动态框架

**静态框架**在编译时链接, 在这种情况下,**动态框架**在运行时链接, 并且可以在不重新链接的情况下修改因此。 如果已使用 iOS 8 之前的任何第三方框架, 则使用的是编译到应用中的**静态框架**。 有关更多详细信息, 请参阅 Apple 的[动态库编程](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)文档。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>嵌入与系统框架

**嵌入框架**包含在应用捆绑包中, 只能通过其沙箱访问特定应用。 **系统框架**存储在操作系统级别, 适用于设备上的所有应用。 目前, 只有 Apple 能够创建操作系统级框架。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>瘦与Fat 框架

**精简框架**只包含特定系统体系结构的已编译代码, 其中**Fat 框架**包含多个体系结构的代码。 编译到框架中的每个特定于体系结构的基本代码称为_切片_。 例如, 如果我们有一个框架, 该框架是针对两个 iOS 模拟器体系结构 (i386 和 X86_64) 编译的, 则它将包含两个切片。

如果你尝试将此示例框架分发到你的应用程序, 它将在模拟器上正确运行, 但在设备上运行失败, 因为框架不包含适用于 iOS 设备的任何代码特定切片。 若要确保框架可用于所有实例, 还需要包含特定于设备的切片, 如 arm64、armv7 和 armv7s。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用嵌入框架

若要在 Xamarin 或 Xamarin 应用程序中使用嵌入框架, 必须完成两个步骤:创建 Fat 框架并嵌入框架。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>创建 Fat 框架

如上所述, 若要在应用程序中使用嵌入框架, 该框架必须为 Fat 框架, 其中包括应用程序将在其上运行的设备的所有系统体系结构切片。

当框架和使用中的应用位于同一个 Xcode 项目中时, 这不是问题, 因为 Xcode 将使用相同的生成设置来构建框架和应用。 由于 Xamarin 应用无法创建嵌入的框架, 因此无法使用此技术。

若要解决此问题, `lipo`可以使用命令行工具将两个或更多框架合并到一个包含所有必需切片的 Fat 框架中。 有关使用`lipo`命令的详细信息, 请参阅[链接本机库](~/ios/platform/native-interop.md)文档。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>嵌入框架

需要执行以下步骤, 才能使用本机引用在 Xamarin 或 Xamarin 项目中嵌入框架:

1. 创建新的或打开现有的 Xamarin、Xamarin 或绑定项目。
2. 在**解决方案资源管理器**中, 右键单击项目名称, 然后选择 "**添加** > " "添加**本机引用**": 

    [![](native-references-images/ref01.png "在解决方案资源管理器中, 右键单击项目名称, 然后选择 \"添加本机引用\"")](native-references-images/ref01.png#lightbox)
3. 从 "**打开**" 对话框中, 选择要嵌入的本机框架的名称, 然后单击 "**打开**" 按钮: 

    [![](native-references-images/ref02.png "选择要嵌入的本机框架的名称, 然后单击 \"打开\" 按钮")](native-references-images/ref02.png#lightbox)
4. 该框架将添加到项目的树中: 

    [![](native-references-images/ref03.png "框架将添加到项目树中")](native-references-images/ref03.png#lightbox)

在编译项目时, 本机框架将嵌入应用程序的捆绑包。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>应用扩展和嵌入式框架

在内部, Xamarin 可能利用此功能以框架的形式与 Mono 运行时链接 (当部署目标为 > = iOS 8.0 时), 从而减少了使用扩展的应用程序的应用大小 (因为仅为提供一次单声道运行时整个应用捆绑包, 而不是一次用于容器应用, 每个扩展一次。

扩展将作为框架与 Mono 运行时链接, 因为所有扩展都需要 iOS 8.0。

没有扩展的应用以及面向 iOS 的应用 

<a name="Summary" />

## <a name="summary"></a>总结

本文详细介绍了如何将本机框架嵌入到 Xamarin 或 Xamarin 应用程序中。

