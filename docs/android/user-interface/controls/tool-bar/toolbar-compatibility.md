---
title: 工具栏兼容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 31602b14179691d13d8058c90cf20a6f7f667124
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522819"
---
# <a name="toolbar-compatibility"></a>工具栏兼容性


## <a name="overview"></a>概述

本部分介绍如何使用`Toolbar`早于 android 5.0 棒糖形的 android 版本。 如果你的应用不支持早于 Android 5.0 的 Android 版本, 则可以跳过此部分。 

由于`Toolbar`属于 android v7 支持库, 因此它可用于运行 Android 2.1 (API 级别 7) 和更高版本的设备上。 但是, 必须安装[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet, 并对代码进行修改, 使其使用此`Toolbar`库中提供的实现。 本部分介绍如何安装此 NuGet 并修改**ToolbarFun**应用程序, 使其不会[添加另一个工具栏](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md), 使其在早于棒糖5.0 的 Android 版本上运行。

若要修改应用以使用工具栏的 AppCompat 版本, 请执行以下操作: 

1. 设置应用的最低和目标 Android 版本。

2. 安装 AppCompat NuGet 包。

3. 使用 AppCompat 主题, 而不是内置的 Android 主题。

4. 修改`MainActivity`以使其成为`AppCompatActivity`子类而`Activity`不是。 

以下各部分将详细介绍其中的每个步骤。



## <a name="set-the-minimum-and-target-android-version"></a>设置最低和目标 Android 版本

应用的目标框架必须设置为 API 级别21或更高版本, 否则将无法正确部署应用。 在部署应用时, 如果在**包 "android" 中找不到属性 "tileModeX" 的任何资源标识符**, 则这是因为目标框架未设置为**ANDROID 5.0 (API 级别为)** 或更高版本。 

将目标框架级别设置为 API 级别21或更高, 并将 Android API 级别项目设置设置为应用支持的最低 Android 版本。 有关设置 Android API 级别的详细信息, 请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。 在本`ToolbarFun`示例中, 最低 Android 版本设置为 KitKat (API 级别为 4.4)。 


## <a name="install-the-appcompat-nuget-package"></a>安装 AppCompat NuGet 包

接下来, 将[Android 支持库 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)包添加到项目。 在 Visual Studio 中, 右键单击 "**引用**", 然后选择 "**管理 NuGet 包 ...** "。单击 "**浏览**", 搜索**Android 支持库 v7 AppCompat**。 选择 " **AppCompat** ", 然后单击 "**安装**": 

[![在 "管理 NuGet 包" 中选择的 V7 Appcompat 包的屏幕截图](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安装此 NuGet 后, 还会安装其他几个 NuGet 包 (如不存在) (如 " **xamarin**"、"支持"、" **xamarin**" 和**Xamarin. Support**)。 有关安装 NuGet 包的详细信息, 请[参阅演练:在项目](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包括 NuGet。 


## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 主题和工具栏

AppCompat 库附带多个`Theme.AppCompat`主题, 可用于 AppCompat 库支持的任何 Android 版本。 示例应用主题是从派生的`Theme.Material.Light.DarkActionBar`, 它在 Android 版本之前的版本中不可用。 `ToolbarFun` 因此, `ToolbarFun`必须改编才能使用本`Theme.AppCompat.Light.DarkActionBar`主题的 AppCompat 对应项。 此外, 由于`Toolbar`在早于棒糖的 Android 版本上不可用, 因此必须使用的 AppCompat `Toolbar`版本。 因此, 布局必须使用`android.support.v7.widget.Toolbar` `Toolbar`而不是。 


### <a name="update-layouts"></a>更新布局

编辑**Resources/layout/main.axml** , 并将`Toolbar`元素替换为以下 XML: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

编辑**资源/布局/toolbar .xml** , 并将其内容替换为以下 xml: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

请注意, `?attr`这些值不再带有`android:`前缀 (回想一下, `?`表示法引用了当前主题中的资源)。 如果`?android:attr`此处仍使用, 则 Android 将从当前运行的平台而不是从 AppCompat 库引用属性值。 由于本示例使用由`actionBarSize` AppCompat 库定义的, 因此将`android:`删除该前缀。 同样, `@android:style`将更改为`@style` , 以使`android:theme`特性设置`ThemeOverlay.AppCompat.Dark.ActionBar`为 AppCompat `ThemeOverlay.Material.Dark.ActionBar`库&ndash;中的主题, 而不是使用主题。 


### <a name="update-the-style"></a>更新样式

编辑**资源/值/样式 .xml** , 并将其内容替换为以下 xml: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

本示例中的项名称和父主题不再带有前缀, `android:`因为我们使用的是 AppCompat 库。 此外, 父主题还会更改为的 AppCompat 版本`Light.DarkActionBar`。 



### <a name="update-menus"></a>更新菜单

为了支持早期版本的 Android, AppCompat 库使用自定义属性来镜像`android:`命名空间的属性。 但是, 在 android api 11 中引入`showAsAction`了某些属性 ( `<menu>`如标记中使用的属性), 这些&ndash; `showAsAction`属性在 android api 11 中已引入, 但在 android api 7 中不可用。 出于此原因, 必须使用自定义命名空间作为支持库定义的所有属性的前缀。 在菜单资源文件中, 定义了一个`local`名为的命名空间`showAsAction` , 用于为属性指定前缀。 

编辑**资源/菜单/top_menus** , 并将其内容替换为以下 xml:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

`local`命名空间添加了以下行:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

属性以此`local:`命名空间开头, 而不是`showAsAction``android:` 

```csharp
local:showAsAction="ifRoom"
```

同样, 编辑**资源/菜单/edit_menus** , 并将其内容替换为以下 xml:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此命名空间开关如何在 API 级别 11 `showAsAction`之前的 Android 版本上提供对该属性的支持？ 安装 AppCompat NuGet `showAsAction`时, 该自定义属性及其所有可能的值都包含在应用中。 


## <a name="subclass-appcompatactivity"></a>子类 AppCompatActivity

转换中的最后一步是修改`MainActivity` , 以便它是的`AppCompactActivity`子类。 编辑**MainActivity.cs**并添加以下`using`语句: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

这声明`Toolbar`为的 AppCompat `Toolbar`版本。 接下来, 更改的`MainActivity`类定义: 

```csharp
public class MainActivity : AppCompatActivity
```

若要将操作栏设置为的`Toolbar`AppCompat 版本, 请将`SetSupportActionBar`对`SetActionBar`的调用替换为。 在此示例中, 还更改了标题, 以指示正在使用的 AppCompat `Toolbar`版本:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最后, 将最低 Android 级别更改为要支持的棒糖之前的值 (例如, API 19)。 

构建应用程序, 并在前棒糖设备或 Android 模拟器上运行它。 以下屏幕截图显示了**ToolbarFun**上运行 KitKat 的 AppCompat 版本: 

[![在 KitKat 设备上运行的应用的完整屏幕截图, 同时显示两个工具栏](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

使用 AppCompat 库时, 无需根据 Android 版本&ndash;切换主题, AppCompat 库使你可以在所有受支持的 Android 版本中提供一致的用户体验。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
