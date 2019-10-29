---
title: 添加第二个工具栏
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 43030d4a221ceadf68c30df2e37449e7f996fa6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029106"
---
# <a name="adding-a-second-toolbar"></a>添加第二个工具栏

## <a name="overview"></a>概述 

`Toolbar` 除了替换操作栏以外，还可以执行更多操作 &ndash; 它可以在活动中的任意位置对其进行自定义，并且可以将其配置为仅跨越屏幕的部分宽度。 下面的示例演示如何创建第二个 `Toolbar`，并将其放置在屏幕底部。 此 `Toolbar` 实现 "**复制**"、"**剪切**" 和 "**粘贴**" 菜单项。 

## <a name="define-the-second-toolbar"></a>定义第二个工具栏 

编辑布局文件**main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

此 XML 会将第二个 `Toolbar` 添加到屏幕的底部，其中空白 `ImageView` 填充屏幕的中间部分。 此 `Toolbar` 的高度设置为操作栏的高度： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

此 `Toolbar` 的背景色设置为将在下一次定义的强调文字颜色：

```xml
android:background="?android:attr/colorAccent
```

请注意，此 `Toolbar` 基于的是不同的主题（**ThemeOverlay ActionBar**），而不是在[替换操作栏](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)时所使用的 `Toolbar`，&ndash; 它未绑定到活动的窗口 décor 或使用的主题第一个 `Toolbar`。

编辑**资源/值/样式 .xml** ，并将以下强调颜色添加到样式定义： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

这为底部工具栏提供了深灰色的琥珀色。 生成和运行应用会在屏幕底部显示空白的第二个工具栏： 

[屏幕底部具有黄色第二个工具栏的应用程序![屏幕截图](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>添加编辑菜单项 

本部分介绍如何将编辑菜单项添加到底部 `Toolbar`。 

将菜单项添加到辅助 `Toolbar`： 

1. 将菜单图标添加到应用项目的 `mipmap-` 文件夹中（如果需要）。

2. 通过将附加的菜单资源文件添加到**资源/菜单**来定义菜单项的内容。 

3. 在活动的 `OnCreate` 方法中，查找 `Toolbar` （通过调用 `FindViewById`）并放大 `Toolbar`的菜单。

4. 为新菜单项在 `OnCreate` 中实现单击处理程序。 

以下部分详细说明了此过程： "**剪切**"、"**复制**" 和 "**粘贴**" 菜单项将添加到底端 `Toolbar`中。 

### <a name="define-the-edit-menu-resource"></a>定义编辑菜单资源

在**资源/菜单**子目录中，创建一个名为**EDIT_MENUS**的新 XML 文件，并将内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此 XML 将创建 "**剪切**"、"**复制**" 和 "**粘贴**" 菜单项（使用已添加到[替换操作栏](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)的 `mipmap-` 文件夹中的图标）。

### <a name="inflate-the-menus"></a>菜单菜单

在**MainActivity.cs**中 `OnCreate` 方法的末尾，添加以下代码行： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

此代码将查找**main.axml**中定义的 `edit_toolbar` 视图，将其标题设置为 "**编辑**"，并增加其菜单项（在**edit_menus**中定义）。 它定义了一个菜单单击处理程序，该处理程序显示 toast 以指示点击了哪个编辑图标。 

生成并运行应用。 应用运行时，将显示上面添加的文本和图标，如下所示： 

[带有剪切、复制和粘贴图标的底部工具栏![关系图](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

点击 "**剪切**" 菜单图标会显示以下 toast： 

[Toast 的![屏幕截图，指示点击了剪切菜单图标](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

点击任一工具栏上的菜单项将显示生成的 toast： 

[用于 "保存"、"复制" 和 "粘贴" 菜单项的 Toast 的屏幕截图![](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>向上键 

大多数 Android 应用依赖于应用导航的 "**后退**" 按钮;按 "**后退**" 按钮会使用户进入上一屏幕。
但是，您可能还需要**提供一个按钮**，使用户可以轻松地导航到应用程序的主屏幕。 当用户选择 "**向上**" 按钮时，用户会在应用层次结构中上移到较高的级别 &ndash; 也就是说，应用程序将在后端堆栈中弹出多个活动，而不是弹出回以前访问的活动。 

若要在第二个使用 `Toolbar` 作为操作栏的活动中启用 "**上移**" 按钮，请调用第二个活动的 `OnCreate` 方法中的 `SetDisplayHomeAsUpEnabled` 和 `SetHomeButtonEnabled` 方法：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[Support V7 Toolbar](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)代码示例演示了操作中的**向上**键。 此示例（使用下面所述的 AppCompat 库）实现第二个活动，该活动使用工具栏上的 "**上移**" 按钮将层次结构导航回上一个活动。 在此示例中，通过进行以下 `SupportActionBar` 方法调用来**启用 "`DetailActivity` home" 按钮**： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

当用户从 `MainActivity` 导航到 `DetailActivity`时，`DetailActivity` 将显示一个 "**向上**" 按钮（左指箭头），如屏幕截图中所示：

[工具栏中的向上按钮左箭头![屏幕快照](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

点击此**按钮**会使应用返回到 `MainActivity`。 在具有多个层次结构级别的更复杂的应用中，点击此按钮会将用户返回到应用中的下一个最高级别，而不是返回到上一屏幕。 

## <a name="related-links"></a>相关链接

- [棒糖形工具栏（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
