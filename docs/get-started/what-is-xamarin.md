---
title: 什么是 Xamarin？
description: 本文介绍 Xamarin 和相关库。
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
ms.openlocfilehash: 34763804e9833224721ea32f9c7e6200dd5faba7
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "75607875"
---
# <a name="what-is-xamarin"></a>什么是 Xamarin？

[![iOS 和 Android 中的示例 Xamarin 应用程序的屏幕截图](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin 是一个开放源代码平台，用于通过 .NET 构建适用于 iOS、Android 和 Windows 的新式高性能应用程序。 Xamarin 是一个抽象层，可管理共享代码与基础平台代码的通信。 Xamarin 在提供便利（如内存分配和垃圾回收）的托管环境中运行。

Xamarin 使开发人员可以跨平台共享其应用程序（平均 90%）。 此模式允许开发人员以一种语言编写所有业务逻辑（或重复使用现有应用程序代码），但在每个平台上实现本机性能和外观。

Xamarin 应用程序可以在电脑或 Mac 上进行编写并编译为本机应用程序包，如 Android 上的 .apk  文件，或 iOS 上的 .ipa  文件。

> [!NOTE]
> 编译和部署适用于 iOS 的应用程序当前需要 MacOS 计算机。 有关开发要求的详细信息，请参阅[系统要求](~/cross-platform/get-started/requirements.md#macos-requirements)。

## <a name="who-xamarin-is-for"></a>Xamarin 的适用对象

Xamarin 适用于具有以下目标的开发人员：

- 跨平台共享代码、测试和业务逻辑。
- 使用 Visual Studio 在 C# 中编写跨平台应用程序。

## <a name="how-xamarin-works"></a>Xamarin 的工作原理

![Xamarin 体系结构示意图](what-is-xamarin-images/xamarin-architecture.png)

该图显示跨平台 Xamarin 应用程序的总体体系结构。 Xamarin 允许在每个平台上创建本机 UI，并在 C# 中编写跨平台共享的业务逻辑。 在大多数情况下，80% 的应用程序代码可使用 Xamarin 进行共享。

Xamarin 是在 Mono  基础上生成的，后者是基于 .NET ECMA 标准的 .NET Framework 的开放源代码版本。 Mono 存在时间几乎与 .NET Framework 本身一样长，并可在大多数平台上运行，包括 Linux、Unix、FreeBSD 和 macOS。 Mono 执行环境会自动处理任务，如内存分配、垃圾回收以及与基础平台的互操作性。

有关特定于平台的体系结构的详细信息，请参阅 [Xamarin.Android](#xamarinandroid) 和 [Xamarin.iOS](#xamarinios)。

### <a name="added-features"></a>添加的功能

Xamarin 结合了本机平台的功能，并添加了一些功能，包括：

1. **基础 SDK 完全绑定** - Xamarin 包含 iOS 和 Android 中几乎整个基础平台 SDK 的绑定。 此外，这些绑定属于强类型，意味着可以轻松导航和使用，并提供可靠的编译时类型检查和开发过程。 强类型绑定可使运行时错误更少，应用程序质量更好。
1. **Objective-C、Java、C 和 C++ 互操作** - Xamarin 提供直接调用 Objective-C、Java、C 和 C++ 库的功能，使用户能够使用各种第三方代码。 通过此功能可以使用采用 Objective-C、Java 或 C/C++ 编写的现有 iOS 和 Android 库。 另外，Xamarin 还提供绑定项目功能，使用户可使用声明性语法绑定本机 Objective-C 和 Java 库。
1. **新式语言构造** - Xamarin 应用程序采用 C# 编写，C# 是一种新式语言，包含针对 Objective-C 和 Java 的重大改进，比如动态语言功能、功能性构造（比如 Lambdas）、LINQ、并行编程和泛型等。
1. **可靠的基类库 (BCL)** - Xamarin 应用程序使用 .NET BCL，后者是类的大量集合，具有全面而简化的功能，比如强大的 XML、数据库、序列化、IO、字符串和网络支持等。 可以编译现有 C# 代码用于应用，这可提供对成千上万的库的访问权限，添加了 BCL 所不包含的功能。
1. **新式集成开发环境 (IDE)** – Xamarin 使用 Visual Studio，后者是一种新式 IDE，其中包括诸如代码自动完成、复杂项目和解决方案管理系统、综合项目模板库、集成源代码管理等功能。
1. **移动跨平台支持** - Xamarin 对 iOS、Android 和 Windows 这三个主要平台提供复杂的跨平台支持。 可编写应用程序共享其多达 90% 的代码，并且 Xamarin.Essentials 提供了一个统一 API 用于跨所有三个平台来访问公共资源。 共享代码可明显减少移动开发人员的开发成本和进行市场推广所需的时间。

### <a name="xamarinandroid"></a>Xamarin.Android

[![Xamarin.Android 体系结构关系图](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin.Android 应用程序从 C# 编译为中间语言 (IL)  ，随后在启动应用程序时，再实时 (JIT)  编译为本机程序集。 Xamarin.Android 应用程序在 Mono 执行环境中与 Android 运行时 (ART) 虚拟机并行运行。 Xamarin 向 Android.* 和 Java.* 命名空间提供 .NET 绑定。 Mono 执行环境通过托管可调用包装器 (MCW)  调入这些命名空间，并向 ART 提供 Android 可调用包装器 (ACW)  ，这使两种环境可以相互调用代码。

有关详细信息，请参阅 [Xamarin.Android 体系结构](~/android/internals/architecture.md)。

### <a name="xamarinios"></a>Xamarin.iOS

[![Xamarin.iOS 体系结构关系图](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Xamarin.iOS 应用程序完全预先 (AOT)  地从 C# 编译为本机 ARM 程序集代码。 Xamarin 使用选择器  向托管 C# 公开 Objective-C，并使用注册器  向 Objective-C 公开托管 C# 代码。 选择器和注册器共同称为“绑定”，使 Objective-C 和 C# 可以进行通信。

有关详细信息，请参阅 [Xamarin.iOS 体系结构](~/ios/internals/architecture.md)。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials 是一个库，可为本机设备功能提供跨平台 API。 与 Xamarin 本身一样，Xamarin.Essentials 是一种简化本机功能访问过程的抽象。 Xamarin.Essentials 提供的功能的一些示例包括：

- 设备信息
- 文件系统
- 加速计
- 电话拨号程序
- 文本到语音转换
- 屏幕锁定

有关详细信息，请参阅 [Xamarin.Essentials](~/essentials/index.md)。

### <a name="xamarinforms"></a>Xamarin.Forms

Xamarin.Forms 是一个开放源代码 UI 框架。 Xamarin.Forms 使开发人员可以从单个共享基本代码生成 iOS、Android 和 Windows 应用程序。 Xamarin.Forms 使开发人员可以 C# 中通过代码隐藏在 XAML 中创建用户界面。 这些用户界面在每个平台上呈现为高性能本机控件。 Xamarin.Forms 提供的功能的一些示例包括：

- XAML 用户界面语言
- 数据绑定
- 笔势
- 效果
- “样式”

有关详细信息，请参阅 [Xamarin.Forms](~/xamarin-forms/index.yml)。

## <a name="get-started"></a>入门

以下指南会帮助你使用 Xamarin 生成第一个应用：

- [Xamarin.Forms 入门](~/xamarin-forms/index.yml)
- [Xamarin.Android 入门](~/android/index.yml)
- [Xamarin.iOS 入门](~/ios/index.yml)
- [Xamarin.Mac 入门](~/mac/index.yml)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
