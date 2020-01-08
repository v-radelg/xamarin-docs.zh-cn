---
title: 什么是 Xamarin。域?
description: 本文介绍 Xamarin。窗体和相关库。
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: c217acdc3bd5c007822cc29fc730df99e373ba01
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607836"
---
# <a name="what-is-opno-locxamarinforms"></a>什么是 Xamarin。域?

[示例的 ![屏幕截图 [！基金.无 LOC （Xamarin）]。IOS 和 Android 中的窗体应用程序](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin。窗体是一种开源 UI 框架。 Xamarin。使用窗体，开发人员可从单个共享代码库生成 Android、iOS 和 Windows 应用程序。

Xamarin。利用窗体，开发人员可以通过中C#的代码隐藏在 XAML 中创建用户界面。 这些接口在每个平台上呈现为高性能本机控件。

## <a name="who-opno-locxamarinforms-is-for"></a>谁 Xamarin。表单适用于

Xamarin。表单适用于具有以下目标的开发人员：

- 跨平台共享 UI 布局和设计。
- 跨平台共享代码、测试和业务逻辑。
- C#用 Visual Studio 编写跨平台的应用程序。

## <a name="how-opno-locxamarinforms-works"></a>如何 Xamarin。窗体有效

![[!基金.无 LOC （Xamarin）]。窗体体系结构关系图](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin。表单提供了一个一致的 API，用于跨平台创建 UI 元素。 此 API 可以在 XAML 或C#中实现，并且支持 VIEWMODEL （MVVM）等模式的数据绑定。

在运行时，Xamarin。Forms 利用平台呈现器将跨平台 UI 元素转换为 Android、iOS 和 UWP 上的本机控件。 允许开发人员在实现跨平台代码共享的优势的同时，获得本机外观和性能。

Xamarin。窗体应用程序通常由共享的 .NET Standard 库和单个平台项目组成。 共享库包含 XAML 或C#视图以及任何业务逻辑，如服务、模型或其他代码。 平台项目包含应用程序所需的任何特定于平台的逻辑或包。

Xamarin。窗体使用 Xamarin 跨平台本地运行 .NET 应用程序。 有关 Xamarin的详细信息，请参阅[什么是 Xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-tools"></a>其他工具

Xamarin。表单包含很大的 NuGet 包生态系统，可向应用程序添加各种功能。 本部分介绍几个常用的 NuGet 包。

### <a name="opno-locxamarinessentials"></a>Xamarin。概述

Xamarin。Essentials 是一个库，它为本机设备功能提供跨平台 Api。 与 Xamarin 本身一样，Xamarin。Essentials 是简化访问本机实用工具过程的抽象。 Xamarin提供的一些实用工具示例。Essentials 包括：

- 设备信息
- 文件系统
- 加速计
- 电话拨号程序
- 文本到语音转换
- 屏幕锁

有关详细信息，请参阅[Xamarin。基础知识](~/essentials/index.md)。

### <a name="shell"></a>Shell

Xamarin。Forms Shell 通过提供大多数应用程序所需的基本功能降低了移动应用程序开发的复杂性。 Shell 提供的功能的一些示例包括：

- 常见导航体验
- 基于 URI 的导航方案
- 集成搜索处理程序

有关详细信息，请参阅[Xamarin。Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平台细节

Xamarin。窗体提供了一个公共 API，该 API 跨平台呈现本机控件，但特定平台的功能可能在其他平台上并不存在。 例如，Android 平台具有用于在 `ListView` 中快速滚动的本机功能，但 iOS 却没有。 Xamarin。窗体平台细节使你可以利用仅在特定平台上可用的功能，而无需创建自定义呈现器或效果。

Xamarin。表单包括预先构建的解决方案，适用于各种特定于平台的功能。 有关详细信息，请参阅：

- [Xamarin。Forms 平台细节](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平台-详细信息](~/xamarin-forms/platform/android/index.md)
- [iOS 平台-详细信息](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台-详细信息](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>材料视觉对象

Xamarin。Forms 材料视觉对象用于将材料设计规则应用到 Xamarin。窗体应用程序。 Xamarin。Forms 材料视觉对象利用视觉对象属性将自定义呈现器选择性地应用于 UI，从而使应用程序在 iOS 和 Android 中具有一致的外观和感觉。

有关详细信息，请参阅[Xamarin。Forms 资料视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>相关链接

- [Xamarin入门。域](~/xamarin-forms/index.yml)
- [Xamarin。概述](~/essentials/index.md)
- [Xamarin。Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin。Forms 资料视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)
