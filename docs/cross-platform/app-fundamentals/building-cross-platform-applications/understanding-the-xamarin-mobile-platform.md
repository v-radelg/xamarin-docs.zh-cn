---
title: 第1部分–了解 Xamarin Mobile 平台
description: 本文档以较高的层次描述 Xamarin 平台，查看编译过程、平台 SDK 访问、代码共享、用户界面创建、可视化设计器等。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 5398d99a64e7f6b75c8b9e418da252852aff1322
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288679"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第1部分–了解 Xamarin Mobile 平台

Xamarin 平台由多个元素组成，这些元素允许你开发适用于 iOS 和 Android 的应用程序：

- language-允许使用熟悉的语法和复杂功能，如泛型、LINQ 和并行任务库。 **C#**
- **Mono .net framework** –提供 Microsoft .net framework 中广泛功能的跨平台实现。
- **编译器**–根据平台生成本机应用（例如 iOS）或集成的 .NET 应用程序和运行时（例如 Android）。 编译器还会为移动部署执行许多优化，如链接到不使用的代码。
- **IDE 工具**– Mac 和 Windows 上的 Visual Studio 允许您创建、生成和部署 Xamarin 项目。

此外，由于基础语言是C# .net framework，因此可将项目构建为共享还可部署到 Windows Phone 的代码。

## <a name="under-the-hood"></a>在后台

虽然 Xamarin 允许你在中C#编写应用程序，并在多个平台上共享相同的代码，但每个系统上的实际实现都非常不同。

## <a name="compilation"></a>编译

C#源在每个平台上以非常不同的方式为本机应用程序：

- **iOS** – C#预先（AOT）编译为 ARM 汇编语言。 .NET framework 包括在内，在链接过程中将去除未使用的类，以减少应用程序大小。 Apple 不允许在 iOS 上生成运行时代码，因此某些语言功能不可用（请参阅[Xamarin IOS 限制](~/ios/internals/limitations.md)）。
- **Android** – C#编译为 IL 并打包为 MonoVM + JIT'ing。 在链接期间，框架中未使用的类将被去除。 应用程序并行运行，并使用 Java/ART （Android 运行时），并通过 JNI 与本机类型交互（请参阅[Xamarin Android 限制](~/android/internals/limitations.md)）。
- **Windows** – C#编译为 IL 并由内置运行时执行，不需要 Xamarin 工具。 使用 Xamarin 指南设计 Windows 应用程序可以更轻松地在 iOS 和 Android 上重复使用代码。
  请注意，通用 Windows 平台还具有一个 **.NET Native**选项，此选项的行为类似于 XAMARIN 的 AOT 编译。


[Xamarin](~/ios/deploy-test/linker.md)和[xamarin](~/android/deploy-test/linker.md)的链接器文档提供了有关编译过程的此部分的详细信息。

运行时 "编译" –应避免生成`System.Reflection.Emit`用–动态生成的代码。

Apple 内核可防止在 iOS 设备上生成动态代码，因此，在 Xamarin 中即时发出代码将无法正常运行。 同样，动态语言运行时功能也不能与 Xamarin 工具一起使用。

某些反射功能可行（例如 Monotouch.dialog 将它用于反射 API，而不是生成代码。

## <a name="platform-sdk-access"></a>平台 SDK 访问

Xamarin 使用熟悉C#的语法使平台特定 SDK 提供的功能易于访问：

- **ios** – Xamarin 公开 Apple 的 CocoaTouch SDK 框架作为命名空间，你可以从C#这些命名空间进行引用。 例如，包含所有用户界面控件的 UIKit 框架可以包含在一个简单`using UIKit;`的语句中。
- **Android** – Xamarin 将 Google 的 Android SDK 公开为命名空间，因此你可以使用 using 语句（如）访问用户界面控件， `using Android.Views;`来引用支持的 SDK 的任何部分。
- **Windows** -windows 应用是使用 Visual Studio 在 windows 上生成的。 项目类型包括 Windows 窗体、WPF、WinRT 和通用 Windows 平台（UWP）。

## <a name="seamless-integration-for-developers"></a>面向开发人员的无缝集成

Xamarin 的优点是，尽管在幕后，Xamarin 和 Xamarin （与 Microsoft 的 Windows Sdk 结合在一起）提供了一个无缝的体验，可以在所有三C#个平台上重复使用这些代码。

业务逻辑、数据库使用情况、网络访问和其他常见函数可以一次写入并在每个平台上重复使用，从而为作为本机应用程序的外观和执行的平台特定用户界面提供基础。

## <a name="integrated-development-environment-ide-availability"></a>集成开发环境（IDE）可用性

可以在 Mac 或 Windows 上的 Visual Studio 中完成 Xamarin 开发。 你选择的 IDE 将由你要面向的平台决定。

由于 Windows 应用程序只能在 Windows 上开发，因此需要为 iOS、Android_和_windows 进行生成，需要 Visual Studio for windows。 但是，可以在 Windows 和 Mac 计算机之间共享项目和文件，因此，可以在 Mac 上生成 iOS 和 Android 应用，稍后可以将共享代码添加到 Windows 项目。

[要求](~/cross-platform/get-started/requirements.md)指南中更详细地讨论了每个平台的开发要求。

### <a name="ios"></a>iOS

开发 iOS 应用程序需要运行 macOS 的 Mac 计算机。 你还可以使用 Visual Studio 在 Visual Studio 中使用 Xamarin 编写和部署 iOS 应用程序。 但是，对于生成和授权目的仍需要 Mac。

必须安装 Apple 的 Xcode IDE 以提供编译器和模拟器进行测试。 你可以免费在自己的设备上[进行](~/ios/get-started/installation/device-provisioning/free-provisioning.md)测试，而是构建用于分发的应用程序（例如 应用商店）你必须加入 Apple 的开发人员计划（每年 $99 美元）。 每次提交或更新应用程序时，都必须先查看和批准该应用程序，然后才能使其可供客户下载。

代码是用 Visual Studio IDE 和屏幕布局编写的，可以通过编程方式生成，也可以在任一 IDE 中用 Xamarin 的 iOS 设计器进行编辑。

有关设置的详细说明，请参阅[Xamarin IOS 安装指南](~/ios/get-started/installation/index.md)。

### <a name="android"></a>Android

Android 应用程序开发需要安装 Java 和 Android Sdk。 这些工具提供了生成、部署和测试所需的编译器、模拟器和其他工具。 Java、Google 的 Android SDK 和 Xamarin 的工具都可以在 Windows 和 macOS 上安装和运行。 建议使用以下配置：

- Windows 10 Visual Studio 2019
- macOS Mojave （10.11 +） with Visual Studio 2019 for Mac

Xamarin 提供了一个统一的安装程序，该安装程序将使用必备的 Java、Android 和 Xamarin 工具（包括用于屏幕布局的可视化设计器）来配置系统。 有关详细说明，请参阅[Xamarin Android 安装指南](~/android/get-started/installation/index.md)。

你可以在实际设备上构建和测试应用程序，而无需使用 Google 的任何许可证，但是，若要通过商店（例如 Google Play、Amazon &amp;或 Barnes Noble）分发应用程序，可能会向操作员收取注册费。 Google Play 将立即发布你的应用程序，而其他存储的审批过程类似于 Apple。

### <a name="windows"></a>Windows

Windows 应用（WinForms、WPF 或 UWP）是通过 Visual Studio 生成的。 它们不会直接使用 Xamarin。 但是， C#可以在 Windows、IOS 和 Android 之间共享代码。
请访问 Microsoft 的[开发人员中心](https://developer.microsoft.com/)，了解 Windows 开发所需的工具。

## <a name="creating-the-user-interface-ui"></a>创建用户界面 (UI)

使用 Xamarin 的主要好处是，应用程序用户界面使用每个平台上的本机控件，创建从以目标-C 或 Java 编写的应用程序（分别适用于 iOS 和 Android）的应用程序。

在应用程序中生成屏幕时，可以在代码中对控件进行布局，也可以使用适用于每个平台的设计工具创建完整的屏幕。

### <a name="create-controls-programmatically"></a>以编程方式创建控件

每个平台都允许使用代码将用户界面控件添加到屏幕。 这可能会非常耗时，因为在对控件位置和大小进行硬编码像素坐标时，可能难以直观显示已完成的设计。

不过，以编程方式创建控件确实有好处，特别是在 iOS 上，用于生成在 iPhone 和 iPad 屏幕大小上以不同方式进行调整或呈现的视图。

### <a name="visual-designer"></a>可视化设计器

每个平台都有不同的方法，可以直观地布局屏幕：

- **ios** – Xamarin 的 Ios 设计器使用拖放功能和属性字段简化了视图构建。 这些视图共同构成了一个情节提要，可以在中访问 **。** 项目中包含的情节提要文件。
- **Android** – Xamarin 提供适用于 Visual Studio 的 Android 拖放 UI 设计器。 Android 屏幕布局保存为 **。** 使用 Xamarin 工具时 main.axml 文件。
- **Windows** -Microsoft 在 Visual Studio 和 Blend 中提供了拖放 UI 设计器。 屏幕布局存储为。XAML 文件。

这些屏幕截图显示每个平台上可用的视觉屏幕设计器：

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "这些屏幕截图显示每个平台上可用的视觉屏幕设计器")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情况下，都可以在代码中引用以可视化方式创建的元素。

### <a name="user-interface-considerations"></a>用户界面注意事项

使用 Xamarin 构建跨平台应用程序的一个主要优点是，他们可以利用本机 UI 工具包向用户提供熟悉的界面。 UI 还将像任何其他本机应用程序一样快速执行。

某些 UI 形式跨多个平台工作（例如，所有三个平台使用类似的滚动列表控件），但为了使应用程序能够 "感受" 权限，UI 应在适当的时候利用特定于平台的用户界面元素。 平台特定的 UI 形式的示例包括：

- **iOS** – "软后退" 按钮的分层导航，位于屏幕底部的选项卡。
- **Android** –硬件/系统-软件后退按钮、操作菜单和屏幕顶部的选项卡。
- **Windows** -windows 应用可以在台式计算机、平板电脑（如 Microsoft Surface）和手机上运行。 例如，Windows 10 设备可能具有硬件后退按钮和动态磁贴。

建议您阅读与目标平台相关的设计准则：

- **iOS** – [Apple 的人体学接口准则](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [Google 的用户界面指南](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** –[适用于 Windows 的用户体验设计指南](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>库和代码重复使用

Xamarin 平台允许跨所有平台重复使用现有C#代码，并将为每个平台本机编写的库集成。

### <a name="c-source-and-libraries"></a>C#源和库

由于 Xamarin 产品使用C#和 .net framework，因此可以在 Xamarin 或 xamarin 项目中重复使用许多现有源代码（开放源代码和内部项目）。 通常，只需将源添加到 Xamarin 解决方案，即可立即工作。 如果使用了不受支持的 .NET framework 功能，可能需要进行一些调整。

可在C# Xamarin 或 xamarin 中使用的源示例包括：SQLite-NET、Newtonsoft.json 和 SharpZipLib。

### <a name="objective-c-bindings--binding-projects"></a>目标-C 绑定 + 绑定项目

Xamarin 提供了一个名为*btouch*的工具，可帮助创建允许在 Xamarin 项目中使用目标 C 库的绑定。 有关如何执行此操作的详细信息，请参阅[绑定目标-C 类型文档](~/cross-platform/macios/binding/binding-types-reference.md)。

可在 Xamarin 中使用的目标-C 库的示例包括：RedLaser 条形码扫描、Google Analytics 和 PayPal 集成。 [Github](https://github.com/mono/monotouch-bindings)上提供了开源 Xamarin iOS 绑定。

### <a name="jar-bindings--binding-projects"></a>.jar 绑定 + 绑定项目

Xamarin 支持在 Xamarin 中使用现有的 Java 库。 有关如何使用的详细信息，请参阅[绑定 Java 库文档](~/android/platform/binding-java-library/index.md)。Xamarin 中的 JAR 文件。

[Github](https://github.com/mono/monodroid-bindings)上提供开源 Xamarin 的绑定。

### <a name="c-via-pinvoke"></a>C via PInvoke

"平台调用" 技术（P/Invoke）允许托管代码（C#）调用本机库中的方法，并支持本机库回调到托管代码。

例如， [SQLITE 网络](https://github.com/praeclarum/sqlite-net)库使用如下所示的语句：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

这将绑定到 iOS 和 Android 中的本机 C 语言 SQLite 实现。
熟悉现有 C API 的开发人员可以构建一组C#类，以映射到本机 API 并利用现有的平台代码。 在 Xamarin 中提供了用于[链接本机库](~/ios/platform/native-interop.md)的文档，类似的原则适用于 xamarin。

### <a name="c-via-cppsharp"></a>C++via CppSharp

Miguel 介绍他[博客](https://tirania.org/blog/archive/2011/Dec-19.html)上的 CXXI （现在称为[CppSharp](https://github.com/mono/CppSharp)）。 直接绑定到C++库的另一种方法是创建 C 包装，并通过 P/Invoke 绑定到。
