---
title: 第一部分-了解 Xamarin 移动平台
description: 本文档介绍了 Xamarin 平台在高级别，查看编译过程中，平台 SDK 访问、 代码共享、 创建用户界面、 可视化设计器、 和的详细信息。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f5008d4986baa0575030e077b66b69ec0a4fad00
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854413"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第一部分-了解 Xamarin 移动平台

Xamarin 平台包含大量元素，可用于开发适用于 iOS 和 Android 的应用程序：

-   **C#语言**– 使你能够使用熟悉的语法和复杂的功能，例如泛型、 LINQ 和并行任务库。
-   **Mono 的.NET framework** – 提供了 Microsoft.NET framework 中的大量功能的跨平台实现。
-   **编译器**-具体取决于平台，生成本机应用 （例如。 iOS) 或集成的.NET 应用程序和运行时 （例如。 Android)。 对于移动的部署，如链接出未使用的代码，编译器还执行很多优化。
-   **IDE 工具**– 在 Mac 和 Windows 上的 Visual Studio，可创建、 生成和部署 Xamarin 项目。

此外，因为基础语言C#使用.NET framework 中，项目可以进行构造以共享也可部署到 Windows Phone 的代码。

## <a name="under-the-hood"></a>揭秘

尽管 Xamarin 允许你编写应用程序C#，并在多个平台之间共享相同的代码，每个系统上的实际实现是非常不同。

## <a name="compilation"></a>编译

C#源的本机应用到每个平台上非常不同的方式使其途径：

-   **iOS** –C#是提前时间的 (AOT) 编译为 ARM 汇编语言。 .NET framework 是包括在内，以减少应用程序的大小将链接期间去除未使用的类。 Apple 不会不允许运行时代码生成在 iOS 上，因此某些语言功能将不可用 (请参阅[Xamarin.iOS 限制](~/ios/internals/limitations.md))。
-   **Android** –C#是编译为 IL，并打包了 MonoVM + JIT'ing。 在链接过程中去除未使用的 framework 中的类。 应用程序运行的并行使用 Java/画 （Android 运行时） 以及与通过 JNI 的本机类型进行交互 (请参阅[Xamarin.Android 限制](~/android/internals/limitations.md))。
-   **Windows** –C#编译为 IL 和执行由内置运行时，并且不需要 Xamarin 工具。 设计 Windows 应用程序以下 Xamarin 的指南，可以轻松将重复使用 iOS 和 Android 上的代码。
  请注意，还具有通用 Windows 平台 **.NET Native**的行为类似于 Xamarin.iOS 的 AOT 编译的选项。


链接器文档[Xamarin.iOS](~/ios/deploy-test/linker.md)并[Xamarin.Android](~/android/deploy-test/linker.md)提供了有关此部分的编译过程的详细信息。

运行时编译 – 生成使用动态代码`System.Reflection.Emit`– 应避免使用。

Apple 的内核会阻止在 iOS 设备上的动态代码生成，因此发出代码上动态在 Xamarin.iOS 中将无法工作。 同样，不能与 Xamarin 工具一起使用的动态语言运行时功能。

某些反射功能起作用 （例如。 MonoTouch.Dialog 它针对使用反射 API），只是不代码生成。

## <a name="platform-sdk-access"></a>访问平台 SDK

Xamarin 可提供特定于平台的 SDK 可以轻松进行访问的功能与熟悉C#语法：

-   **iOS** – Xamarin.iOS 公开 Apple 的 CocoaTouch SDK 框架可以从引用的命名空间为C#。 例如 UIKit 框架包含所有用户界面控件可以包含在一个简单`using UIKit;`语句。
-   **Android** – Xamarin.Android 将 Google 的 Android SDK 公开为命名空间，以便您可以引用包含一个使用的受支持任何的 SDK 部分语句，如`using Android.Views;`访问用户界面控件。
-   **Windows** – 在 Windows 上使用 Visual Studio 构建 Windows 应用。 项目类型包括 Windows 窗体、 WPF、 WinRT 和通用 Windows 平台 (UWP)。

## <a name="seamless-integration-for-developers"></a>面向开发人员的无缝集成

Xamarin 的优点是，尽管存在实质上不同，Xamarin.iOS 和 Xamarin.Android （结合 Microsoft 的 Windows Sdk） 提供无缝的体验进行写入C#可在所有三个平台之间重复使用的代码。

业务逻辑、 数据库的使用情况、 网络访问和其他常用函数可以写入一次，每个平台，用于查找和本机应用程序执行的特定于平台的用户界面提供的基础上重复使用。

## <a name="integrated-development-environment-ide-availability"></a>集成的开发环境 (IDE) 可用性

在 Mac 或 Windows 上的 Visual Studio 中可进行 Xamarin 开发。 选择将由平台 IDE 你想要为目标。

因为仅在 Windows 中，若要生成适用于 iOS 上, 开发 Windows 应用 Android、_和_Windows 需要 Windows 的 Visual Studio。 但是，就可以共享项目和 Windows 和 Mac 计算机，因此可以在 Mac 上生成 iOS 和 Android 应用和更高版本可共享的代码添加到 Windows 项目之间的文件。

中更详细地讨论了每个平台的开发要求[要求](~/cross-platform/get-started/requirements.md)指南。

### <a name="ios"></a>iOS

开发 iOS 应用程序需要运行 macOS 的 Mac 计算机。 此外可以使用 Visual Studio 编写和部署在 Visual Studio 中使用 Xamarin iOS 应用程序。 但是，Mac 仍需要用于生成和许可目的。

必须安装 Apple 的 Xcode IDE 提供的编译器和模拟器进行测试。 可以在自己设备上测试[免费](~/ios/get-started/installation/device-provisioning/free-provisioning.md)，但构建应用程序 （例如分发。 应用商店中） 必须加入 Apple 的开发人员计划 （99 美元每年）。 每次提交或更新的应用程序，它必须是审阅并批准 Apple 才能将其可供客户下载。

使用 Visual Studio IDE 编写代码，可以以编程方式生成或使用 Xamarin 的 iOS 设计器在任一 IDE 中编辑屏幕布局。

请参阅[Xamarin.iOS 安装指南](~/ios/get-started/installation/index.md)有关如何设置的详细说明。

### <a name="android"></a>Android

Android 应用程序开发要求的 Java 和 Android Sdk 安装。 它们提供的编译器、 仿真程序和构建、 部署和测试所需的其他工具。 Java、 Google 的 Android SDK 和 Xamarin 的工具可以所有安装和 Windows 和 macOS 上运行。 建议采用以下配置：

- Windows 10 使用 Visual Studio 2019
- macOS Mojave （10.11 +） 与 Visual Studio 2019 for Mac

Xamarin 提供了一个统一的安装程序，将配置您的系统必备的 Java、 Android 和 Xamarin 的工具 （包括可视化设计器的屏幕布局）。 请参阅[Xamarin.Android 安装指南](~/android/get-started/installation/index.md)有关的详细说明。

您可以生成和测试任何许可证的情况下在真实设备上的应用程序将来自 Google，但是分发通过应用商店应用程序 (如 Google Play、 Amazon 或 Barnes &amp; Noble) 注册费用可能应支付给运算符。 Google Play 将立即，发布您的应用程序，而其他存储具有类似于 Apple 的审批过程。

### <a name="windows"></a>Windows

使用 Visual Studio 构建 Windows 应用 （WinForms、 WPF 或 UWP）。 它们不直接使用 Xamarin。 但是，C#可以在 Windows、 iOS 和 Android 之间共享代码。
请访问 Microsoft 的[开发人员中心](https://developer.microsoft.com/)若要了解有关 Windows 开发所需的工具。

## <a name="creating-the-user-interface-ui"></a>创建用户界面 (UI)

使用 Xamarin 的主要优点是应用程序用户界面使用上创建的应用程序从 OBJECTIVE-C 或 Java 中编写的应用程序无法区分每个平台的本机控件 (适用于 iOS 和 Android 分别)。

应用程序中构建屏幕，可以在代码中控件的版式或创建的每个平台使用可用的设计工具的完整屏幕。

### <a name="create-controls-programmatically"></a>以编程方式创建控件

每个平台允许用户界面控件添加到使用代码的屏幕。 这一点可能会非常耗时，因为它可能很难直观显示已完成的设计时硬编码像素对等控件的位置和大小。

以编程方式创建控件也有好处，尤其是针对 iOS 构建重设大小或以不同的方式跨 iPhone 和 iPad 屏幕大小呈现的视图。

### <a name="visual-designer"></a>可视化设计器

每个平台都有进行直观地对屏幕进行布局的不同方法：

- **iOS** – Xamarin 的 iOS 设计器可帮助构建使用拖放功能和属性字段的视图。 这些视图共同构成了情节提要，并且可访问中 **。情节提要**项目中包含的文件。
- **Android** – Xamarin for Visual Studio 提供了 Android 的拖放 UI 设计器。 Android 屏幕布局都另存为 **。AXML**文件时使用 Xamarin 工具。
- **Windows** – Microsoft 提供了 Visual Studio 和 Blend 中的拖放 UI 设计器。 作为存储屏幕布局。XAML 文件。

这些屏幕截图显示每个平台上可用的可视化屏幕设计器：

 [ ![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "这些屏幕截图显示每个平台上可用的可视化屏幕设计器")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情况下，可以在代码中引用的元素的直观地创建。

### <a name="user-interface-considerations"></a>用户界面的注意事项

使用 Xamarin 生成跨平台应用程序的主要优点是它们可以充分利用本机 UI 工具包，以向用户提供熟悉的界面。 UI 还会作为任何其他本机应用程序快速执行。

某些 UI 隐喻处理跨多个平台 （例如，所有三个平台使用类似的滚动列表控件），但为了使您的应用程序感觉 UI 应充分利用特定于平台的用户界面元素在适当的时候。 特定于平台的 UI 隐喻的示例包括：

- **iOS** – 软后退按钮，使用分层导航选项卡上屏幕的底部。
- **Android** – 硬件/系统软件后按钮、 操作菜单、 在屏幕顶部的选项卡。
- **Windows** – Windows 应用程序可以在台式计算机、 平板电脑 （如 Microsoft Surface) 和手机上运行。 Windows 10 设备可能包括硬件按钮和动态磁贴，例如回复。

建议你阅读与你面向的平台相关的设计准则：

- **iOS** – [Apple 人机接口指南 》](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [Google 的用户界面指南](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [Windows 用户体验设计指南](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>库和重复使用代码

Xamarin 平台允许重复使用现有的C#跨所有平台，以及针对每个平台编写的本机库的集成代码。

### <a name="c-source-and-libraries"></a>C#源和库

由于 Xamarin 产品使用C#和.NET framework 中，很多 （同时打开源和内部项目） 的现有源代码的可重复使用 Xamarin.iOS 或 Xamarin.Android 项目中。 通常源可以只需添加到 Xamarin 解决方案，它将立即生效。 如果已使用不受支持的.NET framework 功能，可能需要一些调整。

示例C#可以 Xamarin.iOS 或 Xamarin.Android 中使用的源包括：SQLite NET、 NewtonSoft.JSON 和 SharpZipLib。

### <a name="objective-c-bindings--binding-projects"></a>OBJECTIVE-C 绑定 + 绑定项目

Xamarin 提供了一个工具，称为*btouch*可帮助创建允许在 Xamarin.iOS 项目中使用的 Objective C 库的绑定。 请参阅[绑定 Objective C 类型文档](~/cross-platform/macios/binding/binding-types-reference.md)有关如何执行此操作的详细信息。

可以在 Xamarin.iOS 中使用的 Objective C 库的示例包括：RedLaser 条形码扫描、 Google Analytics 和 PayPal 的集成。 开放源代码 Xamarin.iOS 绑定位于[github](https://github.com/mono/monotouch-bindings)。

### <a name="jar-bindings--binding-projects"></a>.jar 绑定 + 绑定项目

Xamarin 支持使用在 Xamarin.Android 中的现有 Java 库。 请参阅[绑定 Java 库文档](~/android/platform/binding-java-library/index.md)有关如何使用的详细信息。从 Xamarin.Android 的 JAR 文件。

开放源代码 Xamarin.Android 绑定位于[github](https://github.com/mono/monodroid-bindings)。

### <a name="c-via-pinvoke"></a>通过 PInvoke C

"平台调用"技术 (P/Invoke) 允许托管的代码 (C#) 来调用本机库中的方法，以及对本机库回调入托管代码的支持。

例如， [SQLite NET](https://github.com/praeclarum/sqlite-net)库使用如下语句：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

这会将绑定到本机 iOS 和 Android 中的 C 语言 SQLite 实现。
熟悉现有的 C API 的开发人员可以构建一组C#类将映射到本机 API，并利用现有的平台代码。 没有文档[链接本机库](~/ios/platform/native-interop.md)在 Xamarin.iOS 中类似做法准则也适用于 Xamarin.Android。

### <a name="c-via-cppsharp"></a>C + + 通过 CppSharp

Miguel 介绍 CXXI (现在称为[CppSharp](https://github.com/mono/CppSharp)) 上，他[博客](https://tirania.org/blog/archive/2011/Dec-19.html)。 直接绑定到 c + + 库的替代方法是创建一个 C 包装和通过 P/Invoke 绑定到的。
