---
title: 弹出菜单
description: 如何添加锚定到特定视图的弹出菜单。
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: a5370cfb8a5c4950b361e5f58b253c63f4f1e240
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029178"
---
# <a name="xamarinandroid-popup-menu"></a>Xamarin Android 弹出式菜单

[PopupMenu](xref:Android.Widget.PopupMenu) （也称为_快捷菜单_）是锚定到特定视图的菜单。 在下面的示例中，一个活动包含一个按钮。 用户点击按钮时，将显示三个项目的弹出菜单：

[使用按钮和三项弹出菜单![应用示例](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>创建弹出菜单

第一步是为菜单创建菜单资源文件并将其放在 "**资源/菜单**" 中。 例如，下面的 XML 是在上一个屏幕截图中显示的三项菜单的代码： **Resources/menu/popup_menu**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

接下来，创建一个 `PopupMenu` 实例，并将其定位到其视图。 创建 `PopupMenu`的实例时，将向其构造函数传递对 `Context` 的引用以及该菜单将附加到的视图。 因此，在构造过程中，弹出菜单将锚定到此视图。

在下面的示例中，在按钮的 click 事件处理程序（名为 `showPopupMenu`）中创建 `PopupMenu`。 此按钮也是 `PopupMenu` 所定位到的视图，如下面的代码示例所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

最后，必须将弹出菜单与之前创建的菜单资源一起*放大*。 在下面的示例中，将添加对菜单的[陀螺](xref:Android.Views.LayoutInflater.Inflate*)形方法的调用，并调用其[Show](xref:Android.Widget.PopupMenu.Show)方法来显示它：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>处理菜单事件

当用户选择菜单项时，将引发[MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) click 事件并且将解除菜单。 点击菜单之外的任何位置即可直接将其关闭。 在这两种情况下，当菜单关闭时，将会引发其[DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) 。 下面的代码为 `MenuItemClick` 和 `DismissEvent` 事件添加事件处理程序：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
    menu.Show ();
};
```

## <a name="related-links"></a>相关链接

- [PopupMenuDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)
