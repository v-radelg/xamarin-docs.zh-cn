---
title: Xamarin.Forms Picker
description: Xamarin.Forms 选取器将显示一个项，用户可以从中选择某个项的简短列表。 本文介绍如何使用选取器类来从数据的列表中选择文本项。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
---

# <a name="xamarinforms-picker"></a>Xamarin.Forms Picker

_选取器视图是一个用于选择文本项中的数据列表控件。_

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker)显示的项，用户可以从中选择某个项的短列表。 `Picker` 定义以下属性：

- [`Title`](xref:Xamarin.Forms.Picker.Title) 类型的`string`，其默认值为`null`。
- `TitleColor` 类型的[ `Color` ](xref:Xamarin.Forms.Color)，用于显示的颜色`Title`文本。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 类型的`IList`，源列表的项可以显示，它默认为`null`。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 类型的`int`，默认值为-1 的选定项的索引。
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 类型的`object`，默认情况下的选定的项`null`。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) 类型的[ `Color` ](xref:Xamarin.Forms.Color)，用于显示文本，默认情况下的颜色[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) 类型的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，其默认值为[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) 类型的`string`，其默认值为`null`。
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) 类型的`double`，其默认值为-1.0。

所有属性受[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着它们可以设置的样式，这些属性可以是数据绑定的目标。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)并[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性具有的默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着，它们可以是数据绑定的目标在使用的应用程序[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。 有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

一个[ `Picker` ](xref:Xamarin.Forms.Picker)首次显示时不显示任何数据。 相反的值及其[ `Title` ](xref:Xamarin.Forms.Picker.Title)属性显示为在 iOS 和 Android 平台上的占位符：

[![](images/picker-initial.png "选取器显示的初始")](images/picker-initial-large.png#lightbox "初始选取器显示")

当[ `Picker` ](xref:Xamarin.Forms.Picker)显示提升焦点，其数据和用户可以选择一项：

[![](images/picker-selection.png "选取器中选择项")](images/picker-selection-large.png#lightbox "选取器中选择项")

[ `Picker` ](xref:Xamarin.Forms.Picker)激发[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)当用户选择某个项的事件。 以下所选内容，所选的项显示通过`Picker`:

![](images/picker-after-selection.png "在选择后选取器")

有两种技术用于填充[ `Picker` ](xref:Xamarin.Forms.Picker)数据：

- 设置[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性设置为要显示的数据。 这是推荐采用的方法。 有关详细信息，请参阅[选取器的 ItemsSource 属性设置](populating-itemssource.md)。
- 添加要向显示的数据[ `Items` ](xref:Xamarin.Forms.Picker.Items)集合。 此技术已填充的原始过程[ `Picker` ](xref:Xamarin.Forms.Picker)的数据。 有关详细信息，请参阅[将数据添加到选取器的项集合](populating-items.md)。

## <a name="related-links"></a>相关链接

- [Picker](xref:Xamarin.Forms.Picker)
