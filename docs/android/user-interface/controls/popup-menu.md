---
title: 弹出菜单
description: 如何将锚定的弹出菜单添加到特定视图。
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 1e74c8b7745936f6e9a8890fd26acafe2f2fb6d5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288634"
---
# <a name="popup-menu"></a>弹出菜单

[PopupMenu](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/) (也称为_快捷菜单_) 是一个菜单，定位到特定视图。 在以下示例中，单个活动包含一个按钮。 当用户点击按钮时，将显示三个项弹出菜单：

[![使用按钮和三个项弹出菜单的应用示例](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>创建弹出菜单

第一步是创建菜单的菜单资源文件并将其置于**资源/菜单**。 例如，以下 XML 是在上面的屏幕截图中显示的三个项菜单的代码**Resources/menu/popup_menu.xml**:

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

接下来，创建的实例`PopupMenu`和锚定到其视图。 创建的实例时`PopupMenu`，将其构造函数传递的引用`Context`以及菜单将附加到的视图。 因此，弹出菜单锚定到此视图在其构造过程。

在以下示例中，`PopupMenu`在按钮的单击事件处理程序中创建 (其名为`showPopupMenu`)。 此按钮也是到视图`PopupMenu`定位，如下面的代码示例中所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

最后，必须在弹出菜单*夸大*与先前创建的菜单资源。 在下面的示例中，调用的菜单[充气](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/)方法将添加并将其[显示](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/)调用方法来显示它：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>处理菜单事件

当用户选择菜单项， [MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/)单击将引发事件并将已解除的菜单。 点击菜单外的任意位置将只需关闭它。 在任一情况下，当消除菜单上，其[DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/)将会引发。 下面的代码将事件处理程序添加两个`MenuItemClick`和`DismissEvent`事件：

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

- [PopupMenuDemo （示例）](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
