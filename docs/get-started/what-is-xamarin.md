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
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "75607875"
---
# <a name="what-is-xamarin"></a>什么是 Xamarin？

[iOS 和 Android 中示例 Xamarin 应用程序的 ![屏幕截图](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin 是一个开源平台，用于通过 .NET 构建适用于 iOS、Android 和 Windows 的新式应用程序。 Xamarin 是一个抽象层，可用于管理共享代码与基础平台代码的通信。 Xamarin 在提供很便利（如内存分配和垃圾回收）的托管环境中运行。

Xamarin 使开发人员能够跨平台共享其应用程序的平均90%。 此模式允许开发人员以一种语言（或重复使用现有的应用程序代码）编写所有业务逻辑，但在每个平台上实现本机性能、外观和外观。

可以在电脑或 Mac 上编写 Xamarin 应用程序并将其编译到本机应用程序包中，例如 Android 上的**apk**文件或 iOS 上的**ipa**文件。

> [!NOTE]
> 编译和部署适用于 iOS 的应用程序目前需要 MacOS 机。 有关开发要求的详细信息，请参阅[系统要求](~/cross-platform/get-started/requirements.md#macos-requirements)。

## <a name="who-xamarin-is-for"></a>Xamarin 适用于

Xamarin 适用于具有以下目标的开发人员：

- 跨平台共享代码、测试和业务逻辑。
- C#用 Visual Studio 编写跨平台应用程序。

## <a name="how-xamarin-works"></a>Xamarin 的工作原理

![Xamarin 体系结构示意图](what-is-xamarin-images/xamarin-architecture.png)

此图显示了跨平台 Xamarin 应用程序的总体体系结构。 Xamarin 允许你在每个平台上创建本机 UI，并在中C#编写跨平台共享的业务逻辑。 在大多数情况下，可使用 Xamarin 共享80% 的应用程序代码。

Xamarin 基于 .NET ECMA 标准在**Mono**（.NET Framework 的开源版本）之上构建而成。 Mono 几乎只要 .NET Framework 本身，就会在大多数平台（包括 Linux、Unix、FreeBSD 和 macOS）上运行。 Mono 执行环境会自动处理任务，如内存分配、垃圾回收和与基础平台的互操作性。

有关平台特定体系结构的详细信息，请参阅[xamarin](#xamarinandroid)和[xamarin](#xamarinios)。

### <a name="added-features"></a>新增功能

Xamarin 结合了本机平台的功能并添加了许多功能，包括：

1. **基础 sdk 的完整绑定**– Xamarin 包含适用于 IOS 和 Android 的几乎整个底层平台 sdk 的绑定。 此外，这些绑定属于强类型，意味着可以轻松导航和使用，并提供可靠的编译时类型检查和开发过程。 强类型绑定导致更少的运行时错误和更高质量的应用程序。
1. **目标-C、Java、C 和C++互操作**– Xamarin 提供用于直接调用目标 c、java、c 和C++库的工具，使你能够使用各种第三方代码。 此功能可让你使用以目标-C、Java 或 C/C++编写的现有 IOS 和 Android 库。 此外，Xamarin 还提供绑定项目，使你可以使用声明性语法绑定本机目标-C 和 Java 库。
1. **新式语言构造**– Xamarin 应用程序是以C#一种新式语言编写的，它包括针对客观-C 和 Java 的重大改进，例如动态语言功能、lambda、LINQ、并行编程、泛型等功能。
1. **可靠的基类库（BCL）** – Xamarin 应用程序使用 .net BCL，这是一个大型类的集合，其中包含功能强大的 XML、数据库、序列化、IO、字符串和网络支持等功能的综合性、简化的功能。 可以C#对现有代码进行编译以供在应用程序中使用，从而提供对数千个库的访问权限，这些库添加了 BCL 以外的功能。
1. **新式集成开发环境（IDE）** -Xamarin 使用 Visual Studio，这是一个包含代码自动完成、复杂项目和解决方案管理系统、综合项目模板库、集成源代码管理等功能的新式 IDE。
1. **移动跨平台支持**– Xamarin 为 IOS、Android 和 Windows 的三个主要平台提供了复杂的跨平台支持。 可以编写应用程序来共享其代码的90%，Xamarin 提供了一个统一的 API 来访问所有三个平台上的常见资源。 共享代码可显著降低移动开发人员的开发成本和面市时间。

### <a name="xamarinandroid"></a>Xamarin.Android

[![Xamarin 构架示意图](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin Android 应用程序将编译C#为**中间语言（IL）** ，然后在应用程序启动时，将**实时（JIT）** 编译为本机程序集。 Xamarin Android 应用程序在 Mono 执行环境中与 Android 运行时（ART）虚拟机并行运行。 Xamarin 向 Android. * 和 Java. * 命名空间提供 .NET 绑定。 Mono 执行环境通过**托管可调用包装器（MCW）** 调入这些命名空间，并向该图稿提供**Android 可调用包装（ACW）** ，从而允许两个环境分别调用代码。

有关详细信息，请参阅[Xamarin。](~/android/internals/architecture.md)

### <a name="xamarinios"></a>Xamarin.iOS

[![Xamarin 架构示意图](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Xamarin iOS 应用程序完全**提前（AOT）** 编译C#为本机 ARM 程序集代码。 Xamarin 使用**选择器**向托管C#和**注册机构**公开目标为 c 的托管C#代码。 选择器和注册机构共同称为 "绑定"，并允许目标- C# C 和通信。

有关详细信息，请参阅[Xamarin。](~/ios/internals/architecture.md)

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin 是一个库，它为本机设备功能提供跨平台 Api。 像 Xamarin 本身一样，Xamarin 是简化访问本机功能的过程的抽象。 Xamarin 提供的一些功能示例包括：

- 设备信息
- 文件系统
- 加速计
- 电话拨号程序
- 文本到语音转换
- 屏幕锁

有关详细信息，请参阅[Xamarin](~/essentials/index.md)。

### <a name="xamarinforms"></a>Xamarin.Forms

Xamarin 是一个开源 UI 框架。 使用 Xamarin，开发人员可从单个共享代码库生成 iOS、Android 和 Windows 应用程序。 Xamarin 允许开发人员在 XAML 中通过代码隐藏创建用户界面C#。 这些用户界面在每个平台上呈现为高性能本机控件。 Xamarin 提供的一些功能示例包括：

- XAML 用户界面语言
- 数据绑定
- 笔势
- 效果
- 样式

有关详细信息，请参阅[Xamarin。](~/xamarin-forms/index.yml)

## <a name="get-started"></a>入门

以下指南将帮助你使用 Xamarin 构建你的第一个应用：

- [Xamarin 入门](~/xamarin-forms/index.yml)
- [Xamarin Android 入门](~/android/index.yml)
- [Xamarin iOS 入门](~/ios/index.yml)
- [Xamarin 入门](~/mac/index.yml)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
