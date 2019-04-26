---
title: 本机引用 iOS、 Mac 和绑定项目
description: 本机引用使你能够将本机框架嵌入到 Xamarin.iOS、 Xamarin.Mac、 或绑定项目。
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201528"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>IOS、 Mac 和绑定项目中的本机引用

_本机引用使你能够将本机框架嵌入到 Xamarin.iOS 或 Xamarin.Mac 项目或绑定项目。_

自 iOS 8.0 有了可以创建要应用扩展和 Xcode 中的主应用程序之间共享代码的嵌入式的框架。 使用本机引用功能将有可能在 Xamarin.iOS 中使用这些嵌入式的框架 （使用 Xcode 创建）。
 
> [!IMPORTANT]
> 本机引用不是可以从任何类型的 Xamarin.iOS 或 Xamarin.Mac 项目中创建嵌入的框架，只允许使用现有的本机 (Objective C) 框架。

<a name="Terminology" />

## <a name="terminology"></a>术语

在 iOS 8 （和更高版本），**嵌入式框架**可以同时嵌入静态链接和动态链接的框架。 若要正确地分发它们，您必须将其设置到包含的所有"fat"框架及其_切片_您希望与您的应用程序支持每个设备体系结构。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>静态端口与动态框架

**静态框架**在编译时链接其中**动态框架**链接在运行时，因此可以修改而无需重新链接。 如果已使用 iOS 8 之前的任何第三方框架，已使用**静态 Framework**的已编译到您的应用程序。 请参阅 Apple[动态库编程](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)文档了解详细信息。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>嵌入的 vs。系统框架

**嵌入框架**包含在应用捆绑包中，才可以访问特定应用程序通过其沙盒。 **系统框架**存储在操作系统级别和适用于在设备上的所有应用。 目前，仅 Apple 已创建操作系统级别框架的功能。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>精简 vs。Fat 框架

**细框架**包含适用于特定系统体系结构仅编译的代码位置**Fat 框架**包含多个体系结构的代码。 编译到一个框架，每个特定于体系结构的基本代码称为_切片_。 因此，例如，如果我们有一个框架，为两个 iOS 模拟器体系结构 （i386 和 X86_64） 编译的其中包含两个切片。

如果您尝试将此示例框架与您的应用程序，它会正确运行在模拟器中，但在设备上失败，因为框架不包含任何特定于代码的切片的 iOS 设备。 若要确保在所有情况下，框架将正常工作，它还需要包括 arm64、 armv7 和 armv7s 等特定于设备的切片。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用嵌入式框架

有两个步骤，必须完成才能使用 Xamarin.iOS 或 Xamarin.Mac 应用中的嵌入式框架：创建一个框架，Fat 和嵌入框架。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>创建一个 Fat 框架

如上所述，若要能够使用一个嵌入式框架在应用中，它必须是 Fat 框架，包括所有的设备的应用程序将在上运行的系统体系结构切片。

当框架和正在使用的应用是相同的 Xcode 项目中时，这不是问题作为 Xcode 将生成的框架并使用相同的生成设置的应用。 由于 Xamarin 应用程序不能创建嵌入的框架，不能使用此方法。

若要解决此问题，`lipo`命令行工具可用于将两个或多个框架合并为一个包含所有必要的切片的 Fat 框架。 有关使用的详细信息`lipo`命令，请参阅我们[链接本机库](~/ios/platform/native-interop.md)文档。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>嵌入一个框架

以下步骤需要使用本机引用 Xamarin.iOS 或 Xamarin.Mac 项目中嵌入一个框架：

1. 创建新的或打开现有的 Xamarin.iOS、 Xamarin.Mac 或绑定项目。
2. 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **添加本机引用**: 

    [![](native-references-images/ref01.png "在解决方案资源管理器，右键单击项目名称并选择添加本机引用")](native-references-images/ref01.png#lightbox)
3. 从**开放**对话框框中，选择你想要嵌入，然后单击本机框架的名称**打开**按钮： 

    [![](native-references-images/ref02.png "选择本机框架嵌入，并单击打开按钮的名称")](native-references-images/ref02.png#lightbox)
4. 该框架将添加到项目的树： 

    [![](native-references-images/ref03.png "该框架将添加到项目树")](native-references-images/ref03.png#lightbox)

编译项目时，将在应用的捆绑包中嵌入本机框架。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>应用扩展和嵌入式的框架

在内部 Xamarin.iOS 可能需要利用此功能将其作为一种框架的 Mono 运行时与链接 (时部署目标是 > = iOS 8.0)，从而减少了应用程序大小显著为扩展的应用 （因为将为一次只能包含 Mono 运行时整个应用程序捆绑包，而不是一次用于容器应用程序和每个扩展一次）。

扩展都将链接与 Mono 运行时作为一种框架，因为所有扩展插件需要 iOS 8.0。

未扩展，但应用该面向 iOS 的应用 

<a name="Summary" />

## <a name="summary"></a>总结

本文已嵌入到 Xamarin.iOS 或 Xamarin.Mac 应用程序的本机框架的详细的信息。

