---
title: Xamarin Forms 选取器
description: Xamarin 选取器显示项的简短列表，用户可从中选择项。 本文介绍如何使用选取器类从数据列表中选择文本项。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695992"
---
# <a name="xamarinforms-picker"></a>Xamarin Forms 选取器

_"选取器" 视图是用于从数据列表中选择文本项的控件。_

Xamarin [`Picker`](xref:Xamarin.Forms.Picker)显示一小项列表，用户可从中选择项。 `Picker` 定义以下属性：

- `string` 类型的[`Title`](xref:Xamarin.Forms.Picker.Title) ，默认为 `null`。
- [`Color`](xref:Xamarin.Forms.Color)类型的 `TitleColor`，用于显示 `Title` 文本的颜色。
- `IList` 类型的[`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 、要显示的项的源列表（默认为 `null`）。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)类型 `int`，即选定项的索引，默认值为-1。
- `object` 类型的[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) ，将默认为 `null` 的选定项。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)类型[`Color`](xref:Xamarin.Forms.Color)，用于显示文本的颜色（默认为[`Color.Default`](xref:Xamarin.Forms.Color.Default)）。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)类型的[`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) ，默认为[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string` 类型的[`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) ，默认为 `null`。
- `double` 类型的[`FontSize`](xref:Xamarin.Forms.Picker.FontSize) ，默认值为-1.0。
- `CharacterSpacing` 类型 `double`，是 `Picker` 显示的项的字符之间的间距。

所有属性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以采用样式，属性可以是数据绑定的目标。 [@No__t_1](xref:Xamarin.Forms.Picker.SelectedIndex)和[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)属性的默认绑定模式为[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它们可以是使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中数据绑定的目标。 有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

第一次显示时， [`Picker`](xref:Xamarin.Forms.Picker)不显示任何数据。 相反，其[`Title`](xref:Xamarin.Forms.Picker.Title)属性的值在 IOS 和 Android 平台上显示为占位符：

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

当[`Picker`](xref:Xamarin.Forms.Picker)获得焦点时，将显示其数据，并且用户可以选择项：

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

当用户选择某一项时， [`Picker`](xref:Xamarin.Forms.Picker)将引发[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件。 选择后，`Picker` 将显示选定项目：

![](images/picker-after-selection.png "Picker after Selection")

使用数据填充[`Picker`](xref:Xamarin.Forms.Picker)有两种方法：

- 将[`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)属性设置为要显示的数据。 这是推荐采用的方法。 有关详细信息，请参阅[设置选取器的 System.windows.controls.itemscontrol.itemssource 属性](populating-itemssource.md)。
- 添加要显示到[`Items`](xref:Xamarin.Forms.Picker.Items)集合中的数据。 此方法是使用数据填充[`Picker`](xref:Xamarin.Forms.Picker)的原始过程。 有关详细信息，请参阅[将数据添加到选取器的项集合](populating-items.md)。

## <a name="related-links"></a>相关链接

- [选取器](xref:Xamarin.Forms.Picker)
