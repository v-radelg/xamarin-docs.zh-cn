---
title: Xamarin.Forms 控件模板简介
description: 借助 Xamarin.Forms 控件模板，可在运行时轻松设计或重新设计应用程序页面的主题。 本文介绍控件模板。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 70646999154297592137c6966626b318fb73897c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771264"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Xamarin.Forms 控件模板简介

_借助 Xamarin.Forms 控件模板，可在运行时轻松设计或重新设计应用程序页面的主题。本文介绍控件模板。_

控件具有各种属性，例如 `BackgroundColor` 和 `TextColor`，可用于定义控件外观的各个方面。 可使用[样式](~/xamarin-forms/user-interface/styles/index.md)设置这些属性，以便在运行时进行更改来实现基本主题设置。 但是，样式不能维持页面外观与其内容之间的完全分离，并且通过设置此类属性可以进行的更改是有限的。

使用控件模板可将页面外观与其内容完全分离，因此能够创建可轻松设计主题的页面。 例如，某个应用程序可能包含提供深色主题和浅色主题的应用程序级控件模板。 该应用程序中的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都可以通过应用其中一个控件模板来设计主题，而无需更改每个页面显示的内容。 此外，控件模板提供的主题并不限于更改控件的属性。 它们还可以更改用于实现主题的控件。

## <a name="creating-a-controltemplate"></a>创建 ControlTemplate

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 指定页面或视图的外观，包含根布局，并在该布局内包含用于实现模板的控件。 通常情况下，`ControlTemplate` 将利用 [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 来标记页面或视图要显示的内容的显示位置。 然后，使用 `ControlTemplate` 的页面或视图将定义 `ContentPresenter` 要显示的内容。 下图演示了包含多个控件的页面的 `ControlTemplate`，其中包括由蓝色矩形标记的 `ContentPresenter`：

![](introduction-images/control-template.png "页面的控件模板")

可通过设置以下类型的 `ControlTemplate` 属性，向这些类型应用 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)：

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

创建 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 并将其分配给这些类型时，任何现有外观都将替换为 `ControlTemplate` 中定义的外观。 此外，除了使用 `ControlTemplate` 属性设置外观外，还可以使用样式来应用控件模板，以进一步扩展主题功能。

> [!NOTE]
> *什么是 `TemplatedPage` 和 `TemplatedView` 类型？* `TemplatedPage` 是 `ContentPage` 的基类，是 Xamarin.Forms 提供的最基本的页面类型。 与 `ContentPage` 不同的是，`TemplatedPage` 没有 `Content` 属性。 因此，无法向 `TemplatedPage` 实例直接添加内容， 而是通过设置 `TemplatedPage` 实例的控件模板来添加内容。 同样，`TemplatedView` 是 `ContentView` 的基类。 与 `ContentView` 不同的是，`TemplatedView` 没有 `Content` 属性。 因此，无法向 `TemplatedView` 实例直接添加内容， 而是通过设置 `TemplatedView` 实例的控件模板来添加内容。

可以在 XAML 和 C# 中创建控件模板：

- 在 XAML 中创建的控件模板在分配给页面的 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合（更常见的是分配给应用程序的 [`Resources`](xref:Xamarin.Forms.Application.Resources) 集合）的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定义。
- 在 C# 中创建的控件模板通常在页面的类中定义，或者在可以全局访问的类中定义。

选择在何处定义 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 实例会影响其应用范围：

- 在页面级别定义的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 实例只能应用于页面。
- 在应用程序级别定义的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 实例可以应用于整个应用程序中的页面。

视图层次结构中层次较低的控件模板优先于在较高层次定义的控件模板。 例如，在页面级别定义的名为 `DarkTheme` 的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 将优先于在应用程序级别定义的同名模板。 因此，对于定义要应用于应用程序中每个页面的主题的控件模板，应在应用程序级别进行定义。

## <a name="related-links"></a>相关链接

- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
