---
title: 效果简介
description: 借助效果可对每个平台上的本机控件进行自定义，通常用于细微的样式更改。 本文对效果进行了介绍，概述了效果和自定义呈现器之间的区别，并介绍了 PlatformEffect 类。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9325c34c645b75f28c7e2070f6bb095780ddb02
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "70771453"
---
# <a name="introduction-to-effects"></a>效果简介

_借助效果可对每个平台上的本机控件进行自定义，通常用于细微的样式更改。本文对效果进行了介绍，概述了效果和自定义呈现器之间的区别，并介绍了 PlatformEffect 类。_

Xamarin.Forms [页面、布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供常见的 API，用于描述跨平台的移动用户界面。 通过 `Renderer` 类可以在每个平台上以不同方式呈现每个页面、布局和控件，反过来还可以创建本机控件（相对于 Xamarin.Forms 形式）、在屏幕上排列该控件并添加共享代码中指定的行为。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 但是，实现自定义呈现器类以执行简单的控件自定义通常是大型响应。 效果简化了此过程，可以更轻松地自定义每个平台上的本机控件。

通过子类化 `PlatformEffect` 控件在特定于平台的项目中创建效果，然后通过将它们附加到 Xamarin.Forms .NET 标准库或共享库项目中的适当控件来使用效果。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>为什么要在自定义呈现器上使用效果？

效果简化了控件的自定义、可重用并且可以通过参数化进一步增加重用。

任何可以使用效果达成的事情也可以使用自定义呈现器达成。 但是，自定义呈现器提供比效果更多的灵活性和自定义。 以下指南列出了在自定义呈现器上选择效果的情况：

- 当更改特定于平台的控件的属性能达成所需结果时，建议使用效果。
- 当需要替代特定于平台的控件的方法时，需要使用自定义呈现器。
- 当需要替换实现 Xamarin.Forms 控件的特定于平台的控件时，需要使用自定义呈现器。

## <a name="subclassing-the-platformeffect-class"></a>子类化 PlatformEffect 类

下表列出了 `PlatformEffect` 类在每个平台上的命名空间，以及其属性的类型：

|Platform|命名空间|容器|控件|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|视图|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每个特定于平台的 `PlatformEffect` 类都公开以下属性：

- `Container` – 引用用于实现布局的特定于平台的控件。
- `Control` – 引用用于实现 Xamarin.Forms 控件的特定于平台的控件。
- `Element` – 引用正在呈现的 Xamarin.Forms 控件。

效果没有它们附加到的容器、控件或元素的类型信息，因为它们可以附加到任何元素。 因此，当效果附加到它不支持的元素时，它应该适当地降级或引发异常。 但是，`Container`、`Control` 和 `Element` 属性可以强制转换为其实现的类型。 若要详细了解这些类型，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每个特定于平台的 `PlatformEffect` 类都公开以下方法，必须替代这些方法以实现效果：

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) – 当效果附加到 Xamarin.Forms 控件时调用。 在每个特定于平台的效果类中，此方法的替代版本是执行控件自定义的位置，以及在效果无法应用于指定的 Xamarin.Forms 控件的情况下的异常处理。
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) – 当效果自 Xamarin.Forms 控件分离时调用。 在每个特定于平台的效果类中，此方法的替代版本是执行任何效果清除的位置，例如取消注册事件处理程序。

此外，`PlatformEffect` 公开了 [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) 方法，该方法也可以被替代。 当该元素的属性发生更改时，调用此方法。 在每个特定于平台的效果类中，此方法的替代版本是响应 Xamarin.Forms 控件上的可绑定属性更改的位置。 应始终检查已更改的属性，因为可多次调用此替代。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
