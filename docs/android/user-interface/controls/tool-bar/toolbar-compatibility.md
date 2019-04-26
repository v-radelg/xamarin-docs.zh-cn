---
title: 工具栏兼容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300330"
---
# <a name="toolbar-compatibility"></a>工具栏兼容性


## <a name="overview"></a>概述

本部分介绍如何使用`Toolbar`早于 Android 5.0 Lollipop 版本的 Android 上。 如果您的应用程序不支持早于 Android 5.0 的 Android 版本，则可以跳过本部分中。 

因为`Toolbar`是一部分的 Android v7 支持库，它可以在设备上使用运行 Android 2.1 （API 级别 7） 和更高版本。 但是， [Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)必须在安装 NuGet 和代码修改，使其使用`Toolbar`此库中提供的实现。 本部分介绍如何安装此 NuGet 和修改**ToolbarFun**应用程序从[添加第二个工具栏](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md)，以便它早于棒糖形 5.0 版本的 Android 上运行。

若要修改应用以使用 AppCompat 版本的 Toolbar: 

1.  设置应用程序的最小值和目标 Android 版本。

2.  安装 AppCompat NuGet 包。

3.  而不是内置的 Android 主题使用 AppCompat 主题。

4.  修改`MainActivity`，以便它子类`AppCompatActivity`而非`Activity`。 

每个步骤是以下各节中详细介绍。



## <a name="set-the-minimum-and-target-android-version"></a>设置最小值和目标 Android 版本

为 API 级别 21 或更高版本，必须设置应用的目标框架或应用程序将不能正确部署。 如果如错误**没有资源标识符找到属性 tileModeX 包 android 中**将被同时部署应用，这是因为目标框架未设置为**Android 5.0 (API 级别 21-棒糖形)** 或更高版本。 

设置目标框架级别为 API 级别 21 或更高版本并将 Android API 级别的项目设置设置为应用程序是支持的最低 Android 版本。 有关设置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。 在`ToolbarFun`示例中，最低 Android 版本设置为 KitKat (API 级别 4.4)。 


## <a name="install-the-appcompat-nuget-package"></a>安装 AppCompat NuGet 包

接下来，添加[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)到项目的包。 在 Visual Studio 中，右键单击**引用**，然后选择**管理 NuGet 包...**.单击**浏览**并搜索**Android 支持库 v7 AppCompat**。 选择**Xamarin.Android.Support.v7.AppCompat**然后单击**安装**: 

[![选择管理 NuGet 包中的屏幕截图的 V7 Appcompat 包](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安装此 NuGet 时，多个其他 NuGet 包还会安装如果尚不存在 (如**Xamarin.Android.Support.Animated.Vector.Drawable**， **Xamarin.Android.Support.v4**，并**Xamarin.Android.Support.Vector.Drawable**)。 有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 


## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 主题和工具栏

AppCompat 库提供了几个`Theme.AppCompat`Android AppCompat 库支持的任何版本可用的主题。 `ToolbarFun`示例应用主题派生自`Theme.Material.Light.DarkActionBar`，其上不可用之前的 Android 版本比棒糖形。 因此，`ToolbarFun`必须进行适配化以便 AppCompat 对应用于此主题`Theme.AppCompat.Light.DarkActionBar`。 此外，由于`Toolbar`是不可用的 Android 版本早于棒糖形，我们必须使用的 AppCompat 版本`Toolbar`。 因此，必须使用布局`android.support.v7.widget.Toolbar`而不是`Toolbar`。 


### <a name="update-layouts"></a>更新布局

编辑**Resources/layout/Main.axml** ，并将为`Toolbar`具有下面的 XML 元素： 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

编辑**Resources/layout/toolbar.xml**并将其内容替换为以下 XML: 

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

请注意，`?attr`值不能再带有前缀`android:`(请记住，`?`表示法引用当前主题中的资源)。 如果`?android:attr`仍使用了此处，Android 会引用属性值，从当前正在运行的平台，而不是从 AppCompat 库。 因为此示例使用`actionBarSize`AppCompat 库定义的`android:`删除前缀。 同样，`@android:style`更改为`@style`以便`android:theme`AppCompat 库中将属性设置为主题&ndash;`ThemeOverlay.AppCompat.Dark.ActionBar`此处使用主题而非`ThemeOverlay.Material.Dark.ActionBar`。 


### <a name="update-the-style"></a>更新样式

编辑**Resources/values/styles.xml**并将其内容替换为以下 XML: 

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

项名称和在此示例中的父主题不再带有前缀`android:`因为我们使用 AppCompat 库。 此外，父主题更改为 AppCompat 新版`Light.DarkActionBar`。 



### <a name="update-menus"></a>更新菜单

为了支持早期版本的 Android，AppCompat 库使用镜像的属性的自定义特性`android:`命名空间。 但是，某些属性 (如`showAsAction`属性中使用`<menu>`标记) 在较旧的设备上的 Android framework 中不存在&ndash; `showAsAction` Android API 11 中引入了，但在 Android API 7 中不可用。 出于此原因，必须使用自定义的命名空间前缀的所有支持库定义的属性。 在菜单资源文件，命名空间中调用`local`为前缀定义`showAsAction`属性。 

编辑**Resources/menu/top_menus.xml**并将其内容替换为以下 XML:

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

`local`命名空间添加用下面这行：

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction`属性开头，但这`local:`命名空间而非 `android:` 

```csharp
local:showAsAction="ifRoom"
```

同样，编辑**Resources/menu/edit_menus.xml**并将其内容替换为以下 XML:

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

此命名空间开关如何提供对支持`showAsAction`API 级别 11 之前的 Android 版本上的属性？ 自定义特性`showAsAction`，并且所有可能的值包含在应用程序时 AppCompat NuGet 是否已安装。 


## <a name="subclass-appcompatactivity"></a>子类 AppCompatActivity

在转换过程中的最后一步是修改`MainActivity`，以便它是一个的子类`AppCompactActivity`。 编辑**MainActivity.cs**并添加以下`using`语句： 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

这会将声明`Toolbar`AppCompat 版本的`Toolbar`。 接下来，更改的类定义`MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

若要设置到 AppCompat 版本的操作栏`Toolbar`，替换为调用`SetActionBar`与`SetSupportActionBar`。 在此示例中，标题也会更改以指示 AppCompat 新版`Toolbar`正在使用：

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最后，将最小 Android 级别更改为支持 （例如 API 19） 的预棒糖形值。 

生成应用并在预棒糖形设备或 Android 仿真器上运行。 以下屏幕截图显示了 AppCompat 新版**ToolbarFun**上 Nexus 4 个正在运行 KitKat (API 19): 

[![KitKat 设备上运行的应用的完整屏幕截图，显示这两个工具栏](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

当使用 AppCompat 库时，主题而不必切换根据 Android 版本&ndash;AppCompat 库可以跨所有受支持的 Android 版本提供一致的用户体验。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
