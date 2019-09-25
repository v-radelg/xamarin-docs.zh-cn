---
title: 什么是 Xamarin.Forms？
description: 本文介绍 Xamarin. Forms 和相关库。
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: 8bd530e743330ab8058f13cb7ba09f95a30ee886
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256605"
---
# <a name="what-is-xamarinforms"></a>什么是 Xamarin.Forms？

[![IOS 和 Android 中示例 Xamarin 应用程序的屏幕截图](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin 是一个开源 UI 框架。 使用 Xamarin，开发人员可从单个共享代码库生成 Android、iOS 和 Windows 应用程序。

Xamarin 允许开发人员在 XAML 中通过代码隐藏创建用户界面C#。 这些接口在每个平台上呈现为高性能本机控件。

## <a name="who-xamarinforms-is-for"></a>适用于的 Xamarin 窗体

Xamarin 适用于具有以下目标的开发人员：

- 跨平台共享 UI 布局和设计。
- 跨平台共享代码、测试和业务逻辑。
- C#用 Visual Studio 编写跨平台的应用程序。

## <a name="how-xamarinforms-works"></a>Xamarin 的工作原理

![Xamarin. 窗体体系结构关系图](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin 提供了一个一致的 API，用于跨平台创建 UI 元素。 此 API 可以在 XAML 或C#中实现，并且支持 VIEWMODEL （MVVM）等模式的数据绑定。

在运行时，Xamarin 使用平台呈现器将跨平台 UI 元素转换为 Android、iOS 和 UWP 上的本机控件。 允许开发人员在实现跨平台代码共享的优势的同时，获得本机外观和性能。

Xamarin。窗体应用程序通常由共享的 .NET Standard 库和单个平台项目组成。 共享库包含 XAML 或C#视图以及任何业务逻辑，如服务、模型或其他代码。 平台项目包含应用程序所需的任何特定于平台的逻辑或包。

Xamarin 使用 Xamarin 跨平台本地运行 .NET 应用程序。 有关 Xamarin 的详细信息，请参阅[什么是 xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-tools"></a>其他工具

Xamarin 包含一大的 NuGet 包生态系统，可向应用程序添加各种功能。 本部分介绍几个常用的 NuGet 包。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin 是一个库，它为本机设备功能提供跨平台 Api。 像 Xamarin 本身一样，Xamarin 是简化访问本机实用工具的过程的抽象。 Xamarin 提供的实用程序的一些示例包括：

- 设备信息
- 文件系统
- 加速计
- 电话拨号程序
- 文本到语音转换
- 屏幕锁

有关详细信息，请参阅[Xamarin](~/essentials/index.md)。

### <a name="shell"></a>shell

Xamarin Shell 通过提供大多数应用程序所需的基本功能降低了移动应用程序开发的复杂性。 Shell 提供的功能的一些示例包括：

- 常见导航体验
- 基于 URI 的导航方案
- 集成搜索处理程序

有关详细信息，请参阅[Xamarin。 Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平台细节

Xamarin 提供了一个公共 API，该 API 可跨平台呈现本机控件，但特定平台的功能可能在其他平台上不存在。 例如，Android 平台具有在中`ListView`快速滚动的本机功能，但 iOS 却没有。 Xamarin. 窗体平台说明允许使用仅在特定平台上可用的功能，而无需创建自定义呈现器或效果。

Xamarin 包含用于各种特定于平台的功能的预建解决方案。 有关详细信息，请参见:

- [Xamarin. Forms 平台细节](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平台-详细信息](~/xamarin-forms/platform/android/index.md)
- [iOS 平台-详细信息](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台-详细信息](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>材料视觉对象

Xamarin。窗体材料视觉对象用于将材料设计规则应用于 Xamarin. Forms 应用程序。 Xamarin 格式视觉对象利用视觉对象属性将自定义呈现器选择性地应用于 UI，从而使应用程序在 iOS 和 Android 中具有一致的外观和感觉。

有关详细信息，请参阅[Xamarin. 窗体材料视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>相关链接

- [Xamarin 入门](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin 格式视觉对象](~/xamarin-forms/user-interface/visual/material-visual.md)
