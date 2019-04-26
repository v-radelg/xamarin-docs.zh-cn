---
title: 第 4 部分 - 处理多个平台
description: 本文档介绍如何处理基于平台或功能的应用程序分歧。 它讨论屏幕大小、 导航隐喻、 触控和手势、 推送通知和界面模式，如列表和选项卡。
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8fb373aa5081c8937da5110bad47c3f0decf0126
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275534"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4 部分 - 处理多个平台

## <a name="handling-platform-divergence-amp-features"></a>处理平台分歧&amp;功能

分歧不只是跨平台问题;在相同的平台上的设备具有不同的功能 （尤其是各种可用的 Android 设备）。 最显而易见且最基本是屏幕的大小，但其他设备属性可以改变，并需要应用程序检查有某些功能和行为以不同的方式基于其状态显示 （或不存在）。

这意味着所有应用程序需要处理妥善降级的功能，否则呈现不严重，最低通用标准功能的一组。 Xamarin 的本机 Sdk 与每个平台的深度集成允许利用特定于平台的功能，因此有必要设计应用程序以使用这些功能的应用程序。

请参阅平台有何不同的功能的概述的平台功能文档。

## <a name="examples-of-platform-divergence"></a>平台分歧的示例

### <a name="fundamental-elements-that-exist-across-platforms"></a>存在于跨平台的基本元素

有一些特性是通用的移动应用程序。
以下是设计的更高级别的概念的所有设备通常情况下，因此窗体应用程序的基础：

-  通过选项卡或菜单的功能选择
-  数据和滚动的列表
-  数据的单一视图
-  编辑数据的单一视图
-  向后导航

设计你的高级屏幕流时可以将常见的用户体验基于这些概念。

### <a name="platform-specific-attributes"></a>特定于平台的属性

除了存在于任何平台的基本元素，您将需要您的设计解决关键平台差异。 您可能需要考虑 （和编写代码专门用于处理） 这些差异：

-   **屏幕尺寸**– 某些平台 （如 iOS 和 Windows Phone 的早期版本） 已将其标准化屏幕大小是相对比较简单，若要为目标。 Android 设备具有大量不同的屏幕尺寸，需要更多工作来支持应用程序中。
-   **导航隐喻**– 跨平台 （例如的区别。 硬件后退按钮，全景 UI 控件） 和平台 (Android 2 至 4，iPhone vs iPad) 中。
-   **键盘**– 某些 Android 设备具有物理键盘，而其他用户仅具有软件键盘。 当软键盘遮蔽在屏幕的检测到的代码必须是这些差异敏感。
-   **触控和手势**-操作系统支持手势识别功能各不相同，尤其是在较旧版本的每个操作系统。 早期版本的 Android 具有非常有限的支持触控操作，这意味着支持较旧的设备可能需要单独的代码
-   **推送通知**– （例如每个平台上有不同的功能实现。 动态磁贴在 Windows 上）。

### <a name="device-specific-features"></a>特定于设备的功能

确定应用程序所需的最小功能必须进行的内容;或决定时，若要在每个平台上充分利用哪些其他功能。 将所需的代码来检测功能和禁用功能或 （例如提供多种可选方案。 地理位置的替代方法可以是允许用户键入某一位置，或从映射中进行选择）：

-   **照相机**– 在设备之间的功能有所不同： 某些设备不具有相机、 其他人都具有这两种正面和背面摄像头。 某些摄像机都能使用的视频录制。
-   **地理位置 （& a) 映射**– 支持 GPS 或 Wi-fi 位置不存在的所有设备上。 应用还需要采取措施来适应不同级别的支持的每个方法的准确性。
-   **加速计、 陀螺仪和指南针**– 因此应用程序几乎始终需要提供回退时不支持该硬件通常仅选择的每个平台上的设备中找到这些功能。
-   **Twitter 和 Facebook** – 仅内置 iOS5 和 iOS6 分别。 在早期版本和其他平台上将需要提供您自己的身份验证函数，并直接与每个服务的 API。
-   **附近的通信 (NFC)** – （有些） 仅在 Android 手机 （在撰写本文时）。

## <a name="dealing-with-platform-divergence"></a>面对的平台分歧

从相同的基本代码，每个都有自己的优点和缺点的一组支持多个平台到有两种不同方法。

-   **平台抽象**– 业务外观模式下，提供跨平台的统一的访问和进行了抽象到一个统一的 API 的特定平台实现。
-   **同名的不同实现**– 通过体系结构工具，例如接口以及继承或条件编译的同名的不同实现通过调用特定平台的功能。

## <a name="platform-abstraction"></a>平台抽象

### <a name="class-abstraction"></a>类抽象

使用接口或基类共享代码中定义和实现或扩展特定于平台的项目中。 编写和扩展了类抽象的共享的代码是特别适合到可移植类库因为它们具有提供给他们的框架的有限的子集，并不能包含编译器指令，以支持特定于平台的代码分支。

#### <a name="interfaces"></a>接口

使用接口，可实现仍可以传递到共享库以利用通用代码的特定于平台的类。

在共享的代码中，定义接口，并将其作为参数或属性传递到共享库。

特定于平台的应用程序可以然后实现接口，并且仍然可以利用共享代码处理。

 **优点**

实现可以包含特定于平台的代码，并甚至也不必引用特定于平台的外部库。

 **缺点**

无需创建和将实现传递到共享代码。 如果深度中共享代码使用此接口然后它最终通过多个方法参数传递或否则通过调用链向下推送。 如果共享的代码使用大量不同的接口然后它们必须全部创建和共享代码的某个位置中设置。

#### <a name="inheritance"></a>继承

共享的代码可以在一个或多个特定于平台的项目中实现抽象或虚拟类，无法进行扩展。 这是类似于使用接口，但有一些已实现的行为。 接口或继承是否更好的设计选择有不同视点： 特别是因为 C# 仅允许单一继承它可以规定可以今后设计你的 Api 的方式。 谨慎使用继承。

优点和缺点接口同样适用于继承的基类可以包含一些实现代码 （可能是整个平台不可知实现，可以根据需要扩展） 的其他优点。

## <a name="xamarinforms"></a>Xamarin.Forms

请参阅[Xamarin.Forms](~/get-started/index.yml)文档。

### <a name="other-cross-platform-libraries"></a>其他跨平台库

这些库还提供了跨平台功能的C#开发人员：

- [**Xamarin.Essentials** ](~/essentials/index.md) – 常见功能的跨平台 Api。
- [**SkiaSharp** ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) – 跨平台 2D 图形。

## <a name="conditional-compilation"></a>条件编译

有一些情况下，你的共享的代码将仍需要在每个平台上，可能访问类或行为不同的功能以不同的方式工作。 条件编译最适用于共享资产项目中，在同一源文件中具有不同符号定义的多个项目引用的位置。

Xamarin 项目始终定义`__MOBILE__`适用于 iOS 和 Android 应用程序项目 （请注意双下划线前和上这些符号后修补程序）。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```
#### <a name="ios"></a>iOS

Xamarin.iOS 定义`__IOS__`可以用于检测的 iOS 设备。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

此外，还有特定于监视和电视符号：

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

可以使用下面的代码应仅编译到 Xamarin.Android 应用程序

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每个 API 版本还定义了一个新的编译器指令，因此类似下面的代码将让你添加功能，如果同时针对较新的 API。 每个 API 级别包括所有低的级别符号。 此功能不是真正适用于支持多个平台;通常`__ANDROID__`符号可以是足够。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

当前没有适用于 Xamarin.Mac，内置的符号，但您可以添加自己的 Mac 中的应用程序项目**选项 > 生成 > 编译器**中**定义符号**框中，或编辑 **.csproj**文件，并添加存在 (例如`__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

请使用 `WINDOWS_UWP`。 没有下划线字符，如 Xamarin 平台符号将字符串。

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>使用条件编译

条件编译的一个简单的案例研究示例设置 SQLite 数据库文件的文件位置。 三个平台具有指定的文件位置稍有不同的要求：

-   **iOS** – Apple 首选非用户数据，以将放置在特定位置 （库目录），但没有为此目录没有系统常量。 需要特定于平台的代码来生成正确的路径。
-   **Android** – 返回的系统路径`Environment.SpecialFolder.Personal`是可接受的位置来存储数据库文件。
-   **Windows Phone** – 独立的存储机制不允许完整的路径来指定，只是相对路径和文件名。
-   **通用 Windows 平台**– 使用`Windows.Storage`Api。

下面的代码使用条件编译以确保`DatabaseFilePath`适合于每个平台：

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

结果是可以生成并将 SQLite 数据库文件放在每个平台上的其他位置中的所有平台上使用的类。
