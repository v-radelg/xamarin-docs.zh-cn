---
title: 在 Xamarin.Forms 中的文本
description: Xamarin.Forms 具有三个主要视图，用于处理文本，并且此文章介绍了如何使用它们来输入和 Xamarin.Forms 应用程序中显示的文本。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
ms.openlocfilehash: 8754f6573b00334fd228b1c9bd094cd91565976c
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557085"
---
# <a name="text-in-xamarinforms"></a>在 Xamarin.Forms 中的文本

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_使用 Xamarin.Forms 来输入或显示文本。_

Xamarin.Forms 具有用于处理文本的三个主要视图：

- **[标签](#Label)** &mdash;来显示单个或多行文本。 可以在同一行中显示多个格式设置选项的文本。
- **[条目](#Entry)** &mdash;用于输入只有一行的文本。 条目都具有一种密码模式。
- **[编辑器](#Editor)** &mdash;用于输入文本，可能也需要多个行。

可以使用内置或自定义更改文本外观[样式](#Styles)和某些控件支持的自定义[字体](#Fonts)。

<a name="Label" />

## <a name="labellabelmd"></a>[标签](label.md)

`Label`视图用于显示文本。 它可以显示多行文本或单个文本行。 `Label` 可以提供具有多个中内嵌使用的格式设置选项的文本。 标签视图可以包装或截断时它不适合在同一行的文本。

![](images/label.png "标签示例")

请参阅[标签](label.md)文章更多详细信息。

有关自定义标签中使用的字体的信息，请参阅[字体](fonts.md)。

<a name="Entry" />

## <a name="entryentrymd"></a>[项](entry.md)

`Entry` 用来接受的单行文本输入。 `Entry` 产品/服务控制颜色和字体。 `Entry` 具有密码模式，可以显示占位符文本，直到输入文本。

![](images/entry.png "条目示例")

请参阅[条目](entry.md)文章了解详细信息。

请注意，与不同`Label`，`Entry`不能具有自定义字体设置。

<a name="Editor" />

## <a name="editoreditormd"></a>[编辑器](editor.md)

`Editor` 用于接受多行文本输入。 `Editor` 产品/服务控制颜色和字体。

![](images/editor.png "编辑器示例")

请参阅[编辑器](editor.md)文章了解详细信息。

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[字体](fonts.md)

许多控件都支持不同的字体设置，请在每个平台或您的应用程序中包含的自定义字体使用内置的字体。 请参阅[字体](fonts.md)文章更多详细信息。

<a name="Styles" />

## <a name="stylesstylesmd"></a>[样式](styles.md)

请参阅[使用样式](~/xamarin-forms/user-interface/styles/index.md)若要了解如何设置字体[颜色](~/xamarin-forms/user-interface/colors.md)，和其他适用于多个控件的显示属性。

## <a name="related-links"></a>相关链接

- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
